<template>
  <modal-screen title="Login to æternity" :redirectToOnClose="{ name: 'intro' }">
    <form @submit.prevent="unlockSavedKeystore">
      <ae-label
        :for="_uid"
        help-type="dramatic"
        :help-text="errors.first('password')"
      >Enter your password</ae-label>
      <ae-input
        :id="_uid"
        name="password"
        type="password"
        v-model="password"
        v-validate="'required|min:4'"
        v-focus="true"
      />
      <ae-button
        type="exciting"
        :inactive="errors.any()"
      >Login</ae-button>
    </form>

    <div slot="footer">
      <p>
        Recover with phrase if you forgot your password or
        create a new account if you haven’t done that yet
      </p>
      <ae-button :to="{ name: 'recover' }" size="small" plain type="dramatic" uppercase>
        Recover with passphrase
      </ae-button>
      <ae-button :to="{ name: 'new-account' }" size="small" plain type="exciting" uppercase>
        Create new account
      </ae-button>
    </div>
  </modal-screen>
</template>

<script>
  import { AeLabel, AeInput, AeButton } from '@aeternity/aepp-components'
  import ModalScreen from '@/components/ModalScreen'

  export default {
    components: { ModalScreen, AeInput, AeLabel, AeButton },
    data () {
      return { password: '' }
    },
    methods: {
      async unlockSavedKeystore () {
        if (!await this.$validator.validateAll()) return
        const { keystore } = this.$store.getters

        const derivedKey = await keystore.keyFromPasswordAsync(this.password)
        if (!keystore.isDerivedKeyCorrect(derivedKey)) {
          this.$store.dispatch('setNotification', {
            text: 'You\'ve entered a wrong password',
            icon: require(`emoji-datasource-apple/img/apple/64/1f925.png`),
            autoClose: true
          })
          return
        }
        this.$store.commit('setDerivedKey', derivedKey)
      }
    }
  }
</script>
