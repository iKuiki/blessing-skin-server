<template>
  <section class="content">
    <email-verification />
    <div class="row">
      <div class="col-md-6">
        <div v-once class="box box-primary">
          <div class="box-header with-border">
            <h3 v-t="'user.profile.avatar.title'" class="box-title" />
          </div><!-- /.box-header -->
          <!-- eslint-disable-next-line vue/no-v-html -->
          <div class="box-body" v-html="$t('user.profile.avatar.notice')" /><!-- /.box-body -->
          <div class="box-footer">
            <button
              v-t="'user.resetAvatar'"
              class="btn btn-primary pull-right"
              data-test="resetAvatar"
              @click="resetAvatar"
            />
          </div>
        </div>

        <div class="box box-warning">
          <div class="box-header with-border">
            <h3 v-t="'user.profile.password.title'" class="box-title" />
          </div><!-- /.box-header -->
          <div class="box-body">
            <div class="form-group">
              <label v-t="'user.profile.password.old'" />
              <input
                ref="oldPassword"
                v-model="oldPassword"
                type="password"
                class="form-control"
              >
            </div>

            <div class="form-group">
              <label v-t="'user.profile.password.new'" />
              <input
                ref="newPassword"
                v-model="newPassword"
                type="password"
                class="form-control"
              >
            </div>

            <div class="form-group">
              <label v-t="'user.profile.password.confirm'" />
              <input
                ref="confirmPassword"
                v-model="confirmPassword"
                type="password"
                class="form-control"
              >
            </div>
          </div><!-- /.box-body -->
          <div class="box-footer">
            <button
              v-t="'user.profile.password.button'"
              class="btn btn-primary"
              data-test="changePassword"
              @click="changePassword"
            />
          </div>
        </div><!-- /.box -->
      </div>
      <div class="col-md-6">
        <div class="box box-primary">
          <div class="box-header with-border">
            <h3 v-t="'user.profile.nickname.title'" class="box-title" />
          </div><!-- /.box-header -->
          <div class="box-body">
            <div class="form-group has-feedback">
              <input
                ref="nickname"
                v-model="nickname"
                type="text"
                class="form-control"
                :placeholder="$t('user.profile.nickname.rule')"
              >
              <span class="glyphicon glyphicon-user form-control-feedback" />
            </div>
          </div><!-- /.box-body -->
          <div class="box-footer">
            <button
              v-t="'general.submit'"
              class="btn btn-primary"
              data-test="changeNickName"
              @click="changeNickName"
            />
          </div>
        </div>

        <div class="box box-warning">
          <div class="box-header with-border">
            <h3 v-t="'user.profile.email.title'" class="box-title" />
          </div><!-- /.box-header -->
          <div class="box-body">
            <div class="form-group has-feedback">
              <input
                ref="email"
                v-model="email"
                type="email"
                class="form-control"
                :placeholder="$t('user.profile.email.new')"
              >
              <span class="glyphicon glyphicon-envelope form-control-feedback" />
            </div>
            <div class="form-group has-feedback">
              <input
                ref="currentPassword"
                v-model="currentPassword"
                type="password"
                class="form-control"
                :placeholder="$t('user.profile.email.password')"
              >
              <span class="glyphicon glyphicon-lock form-control-feedback" />
            </div>
          </div><!-- /.box-body -->
          <div class="box-footer">
            <button
              v-t="'user.profile.email.button'"
              class="btn btn-warning"
              data-test="changeEmail"
              @click="changeEmail"
            />
          </div>
        </div>

        <div class="box box-danger">
          <div class="box-header with-border">
            <h3 v-t="'user.profile.delete.title'" class="box-title" />
          </div><!-- /.box-header -->
          <div class="box-body">
            <template v-if="isAdmin">
              <p v-t="'user.profile.delete.admin'" />
              <button v-t="'user.profile.delete.button'" class="btn btn-danger" disabled />
            </template>
            <template v-else>
              <p v-t="{ path: 'user.profile.delete.notice', args: { site: siteName } }" />
              <button
                v-t="'user.profile.delete.button'"
                class="btn btn-danger"
                data-toggle="modal"
                data-target="#modal-delete-account"
              />
            </template>
          </div><!-- /.box-body -->
        </div>
      </div>
    </div>

    <div
      id="modal-delete-account"
      class="modal modal-danger fade"
      tabindex="-1"
      role="dialog"
    >
      <div class="modal-dialog">
        <div class="modal-content">
          <div class="modal-header">
            <button
              type="button"
              class="close"
              data-dismiss="modal"
              aria-label="Close"
            >
              <span aria-hidden="true">&times;</span>
            </button>
            <h4 v-t="'user.profile.delete.modal-title'" class="modal-title" />
          </div>
          <div class="modal-body">
            <div v-once v-text="nl2br($t('user.profile.delete.modal-notice'))" />
            <br>
            <input
              v-model="deleteConfirm"
              type="password"
              class="form-control"
              :placeholder="$t('user.profile.delete.password')"
            >
            <br>
          </div>
          <div class="modal-footer">
            <button
              v-t="'general.close'"
              type="button"
              class="btn btn-outline"
              data-dismiss="modal"
            />
            <a
              v-t="'general.submit'"
              class="btn btn-outline"
              data-test="deleteAccount"
              @click="deleteAccount"
            />
          </div>
        </div><!-- /.modal-content -->
      </div><!-- /.modal-dialog -->
    </div><!-- /.modal -->
  </section><!-- /.content -->
</template>

<script>
import toastr from 'toastr'
import { swal } from '../../js/notify'
import EmailVerification from '../../components/EmailVerification.vue'

export default {
  name: 'Profile',
  components: {
    EmailVerification,
  },
  data: () => ({
    oldPassword: '',
    newPassword: '',
    confirmPassword: '',
    nickname: '',
    email: '',
    currentPassword: '',
    deleteConfirm: '',
    siteName: blessing.site_name,
    isAdmin: blessing.extra.admin,
  }),
  methods: {
    nl2br: str => str.replace(/\n/g, '<br>'),
    async resetAvatar() {
      const { dismiss } = await swal({
        title: this.$t('user.resetAvatarConfirm'),
        type: 'question',
        showCancelButton: true,
      })
      if (dismiss) {
        return
      }

      const { msg } = await this.$http.post(
        '/user/profile/avatar',
        { tid: 0 }
      )
      toastr.success(msg)
      Array.from(document.querySelectorAll('[alt="User Image"]'))
        .forEach(el => (el.src += `?${new Date().getTime()}`))
    },
    async changePassword() {
      const {
        oldPassword, newPassword, confirmPassword,
      } = this

      if (!oldPassword) {
        toastr.info(this.$t('user.emptyPassword'))
        this.$refs.oldPassword.focus()
        return
      }

      if (!newPassword) {
        toastr.info(this.$t('user.emptyNewPassword'))
        this.$refs.newPassword.focus()
        return
      }

      if (!confirmPassword) {
        toastr.info(this.$t('auth.emptyConfirmPwd'))
        this.$refs.confirmPassword.focus()
        return
      }

      if (newPassword !== confirmPassword) {
        toastr.info(this.$t('auth.invalidConfirmPwd'))
        this.$refs.confirmPassword.focus()
        return
      }

      const { errno, msg } = await this.$http.post(
        '/user/profile?action=password',
        { current_password: oldPassword, new_password: newPassword }
      )
      if (errno === 0) {
        await swal({ type: 'success', text: msg })
        return (window.location = `${blessing.base_url}/auth/login`)
      }
      return swal({ type: 'warning', text: msg })
    },
    async changeNickName() {
      const { nickname } = this

      if (!nickname) {
        return swal({ type: 'error', text: this.$t('user.emptyNewNickName') })
      }

      const { dismiss } = await swal({
        text: this.$t('user.changeNickName', { new_nickname: nickname }),
        type: 'question',
        showCancelButton: true,
      })
      if (dismiss) {
        return
      }

      const { errno, msg } = await this.$http.post(
        '/user/profile?action=nickname',
        { new_nickname: nickname }
      )
      if (errno === 0) {
        Array.from(document.querySelectorAll('.nickname'))
          .forEach(el => (el.textContent = nickname))
        return swal({ type: 'success', text: msg })
      }
      return swal({ type: 'warning', text: msg })
    },
    async changeEmail() {
      const { email } = this

      if (!email) {
        return swal({ type: 'error', text: this.$t('user.emptyNewEmail') })
      }

      if (!/\S+@\S+\.\S+/.test(email)) {
        return swal({ type: 'warning', text: this.$t('auth.invalidEmail') })
      }

      const { dismiss } = await swal({
        text: this.$t('user.changeEmail', { new_email: email }),
        type: 'question',
        showCancelButton: true,
      })
      if (dismiss) {
        return
      }

      const { errno, msg } = await this.$http.post(
        '/user/profile?action=email',
        { new_email: email, password: this.currentPassword }
      )
      if (errno === 0) {
        await swal({ type: 'success', text: msg })
        return (window.location = `${blessing.base_url}/auth/login`)
      }
      return swal({ type: 'warning', text: msg })
    },
    async deleteAccount() {
      const { deleteConfirm: password } = this

      if (!password) {
        return swal({ type: 'warning', text: this.$t('user.emptyDeletePassword') })
      }

      const { errno, msg } = await this.$http.post(
        '/user/profile?action=delete',
        { password }
      )
      if (errno === 0) {
        await swal({
          type: 'success',
          text: msg,
        })
        window.location = `${blessing.base_url}/auth/login`
      } else {
        return swal({ type: 'warning', text: msg })
      }
    },
  },
}
</script>