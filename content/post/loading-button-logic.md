---
layout: post
title: "Loading Button Logic"
date: 2018-07-07
---

Here --> in the [Vuetify docs](https://vuetifyjs.com/en/components/buttons#example-loaders) there's a handy prop for turning a button click into a "Loading" animation.  This loading animation can take [many forms](https://vuetifyjs.com/en/components/buttons#example-loaders) but I found that adding a watcher, the loader() function, was a not exactly what I needed in my component.  The function example `loader()` works by setting whatever loader was clicked to the opposite of what it originally was.  Once the 3 second timer expires it'll be set to false.

## The Example: ##
```javascript
<script>
  export default {
    data () {
      return {
        loader: null,
        loading: false,
        loading2: false,
        loading3: false,
        loading4: false
      }
    },
    watch: {
      loader () {
        const l = this.loader
        this[l] = !this[l]

        setTimeout(() => (this[l] = false), 3000)

        this.loader = null
      }
    }
  }
</script>
```

## Why show the user a loader?
This is an easy answer as we've all been in this situation.  You click something and the data has to be sent off and processed.  If you click the button you intuitively expect some sort of feedback telling you that the action is in progress.  Or you want to find out as fast as possible that the action failed.  Without feedback we'd just be mashing the button repeatedly out of frustration.  I might be projecting and maybe that's just me getting frustrated and mashing buttons but I think its a necessity for UIs at this time.

## My API call with a loader:
My button is a login button in this example.  While my backend is checking the database and verifying you are who you say you are the frontend should be inaccessible.  You should immediately know that I've sent of this request to the application and you'll know what to do next in a few seconds (hopefully less).

```javascript
    loginMember: function (e) {
      let self = this
      let headerToken // Check stuff in the header (this may not apply to you)
      this.loading = true
      fetch(apiURL, {
        method: 'POST',
        credentials: 'include',
        headers: {
          'X-CSRF-Token': this.token // We're putting the CSRF token in the header for the server to verify
        },
        body: JSON.stringify({email: this.email, password: this.password}) // Sending the credentials over to the server
      })
        .then(function (response) {
          headerToken = response.headers.get('X-CSRF-Token')
          if (response.status === 403) {
            self.errors = "Please reload.  It looks like you're missing a cookie."  // Something went wrong with the CSRF stuff, reload
            console.log(self.errors) // log if you're testing locally
            return response.status
          }
          return response.json()
        })
        .then(response => {
          if (response['Status'] !== 'OK') { // Uh-oh something was wrong with the credentials
            self.loading = false // Stop the loading animation so the user knows the process is done
            self.errors = response['Status'] // Grab the errors and show them to the user
          } else { // No problems!  User is now logged in and we can do login stuff
            // redirect to signup URL and save user values to vuex store
            console.log(response) //Again, local testing output can be helpful
            this.errors = [] // No errors!
            self.setCSRFToken(headerToken) // May not apply to you as most frameworks handle CSRF tokens
            this.password = ''
            this.logMemberIn()
            this.setMemberEmail(response['Email'])
            this.setMemberName(response['Name'])
            this.setMemberId(response['ID'])
            self.loading = false
            if (this.$route.query.redirect !== undefined) { // If we had a predestined URL in the query route them there
              this.$router.push(this.$route.query.redirect)
            } else {
              this.$router.push('/')  // Otherwise jus send them to the home
            }
          }
        })
    }
```

The above works only because I know what the API values will return.  Although I use standard HTTP statuses there may be a certain format for the body of the error.  The `errors[]` array will hold everything returned and spit it out to the user in a separate function.  Here we'll just assume the user logged in fine and is now redirecting to the URL they wanted!
