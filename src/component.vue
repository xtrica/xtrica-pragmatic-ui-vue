<script>
const XtricaNotify = require('xtrica-notify').default
const XtricaProgress = require('xtrica-progress').default
export default {
  template: require('./template.html'),
  props: ['action','views','onSubmit','onCancel','onRetry','onAbort'],
  data () {
    return {
      notifications: null,
      progress: null,
      viewsDefault: {
        form: require('./form.vue').default,
        loading: require('./loading.vue').default,
        failure: require('./failure.vue').default,
        success: require('./success.vue').default
      }
    }
  },
  computed: {
    viewForm () {
      return this.views.form ? this.views.form : this.viewsDefault.form
    },
    viewLoading () {
      return this.views.loading ? this.views.loading : this.viewsDefault.loading
    },
    viewFailure () {
      return this.views.failure ? this.views.failure : this.viewsDefault.failure
    },
    viewSuccess () {
      return this.views.success ? this.views.success : this.viewsDefault.success
    }
  },
  mounted () {
    this.progress = new XtricaProgress(document.body, 'progress', 650)
    this.notifications = new XtricaNotify(document.body)
  },
  beforeDestroy () {
    this.notifications.cleanup()
    this.progress.cleanup()
  },
  watch: {
    'action.status' (newStatus, oldStatus) {
      if (newStatus !== oldStatus) {
        switch (true) {
          case (newStatus.loading === true): {
            this.notifications.clear()
            this.progress.element.className = 'progress'
            this.progress.setProgress(90)
            break
          }
          case (newStatus.warning === true): {
            let warnings = this.action.getWarnings()
            if (warnings.length) {
              for (let w = 0; w < warnings.length; w++) {
                if (warnings[w].message !== false && warnings[w].message.length) {
                  this.notifications.notify('<p>' + warnings[w].message + '</p>', 'alert' + (this.action.status.error > -1 ? ' failure' : ' warning'), 6000)
                  break
                }
              }
            } else {
              this.notifications.clear()
            }
            if (this.action.status.error > -1) {
              this.progress.element.className = 'progress failure'
            } else {
              this.progress.element.className = 'progress warning'
            }
            break
          }
          case (newStatus.failure === true): {
            this.notifications.clear()
            this.progress.element.className = 'progress failure'
            this.progress.setProgress(0)
            break
          }
          case (newStatus.success === true): {
            this.notifications.clear()
            this.progress.element.className = 'progress success'
            this.progress.setProgress(100).then(() => {
              this.progress.setProgress(-1)
            })
            break
          }
          default: {
            this.notifications.clear()
            this.progress.clear()
          }
        }
      }
    }
  }
}
</script>
