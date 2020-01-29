<template>
    <div :class="$style.registration">
        <div :class="$style.bgLeft"></div>
        <div :class="$style.bgRight"></div>
        <div class="container container_desk pos-r">

        <header :class="$style.header">
            <mnogo-logo></mnogo-logo>
            <go-login></go-login>
        </header>

        <main :class="$style.main">
            <div :class="$style.mainLeft">
                <div :class="$style.registrationForm">
                    <div :class="$style.input">
                        <ui-input v-model="values.username"
                                  :error="Boolean(errors.username)"
                                  name="username"
                                  placeholder="Имя"
                                  @input="errors.username = null">
                        </ui-input>
                    </div>
                    <div :class="$style.input">
                        <ui-input v-model="values.usersurname"
                                  :error="Boolean(errors.usersurname)"
                                  name="usersurname"
                                  placeholder="Фамилия"
                                  @input="errors.usersurname = null">
                        </ui-input>
                    </div>
                    <div :class="$style.input">
                        <ui-input v-model="values.email"
                                  :error="Boolean(errors.email)"
                                  name="email"
                                  placeholder="Email"
                                  @input="errors.email = null">
                        </ui-input>
                    </div>
                    <div :class="$style.input">
                        <ui-input v-model="values.phone"
                                  :error="Boolean(errors.phone)"
                                  name="phone"
                                  placeholder="Телефон"
                                  mask="phone"
                                  @input="errors.phone = null">
                        </ui-input>
                    </div>
                    <div :class="$style.input">
                        <ui-input v-model="values.datebirth"
                                  :error="Boolean(errors.datebirth)"
                                  name="datebirth"
                                  placeholder="Дата рождения"
                                  @input="errors.datebirth = null">
                        </ui-input>
                    </div>
                    <div :class="$style.input">
                        <ui-input v-model="values.password"
                                  :error="Boolean(errors.password)"
                                  name="password"
                                  placeholder="Пароль"
                                  type="password"
                                  mask="password"
                                  @input="errors.password = null">
                        </ui-input>
                    </div>
                </div>
                <div :class="$style.social">
                    <social-login></social-login>
                </div>
            </div>
            <div :class="$style.mainRight">
                <h2 :class="$style.title" class="title-h1">Получайте призы в клубе Много.ру</h2>
                <h3 :class="$style.subtitle" class="text">Вступайте в Клуб Много.ру и начинайте получать призы за покупки уже сейчас. Это бесплатно и займёт всего пару минут.</h3>
            </div>
        </main>
        </div>
    </div>
</template>

<script>
    import mnogoLogo from '~/components/registration/mnogoLogo.vue';
    import goLogin from '~/components/registration/goLogin.vue';
    import socialLogin from '~/components/registration/socialLogin.vue';
    import uiInput from '~/components/ui/uiInput';

    export default {

        components: {
            mnogoLogo,
            goLogin,
            socialLogin,
            uiInput
        },

        props: {
            data: Object,
        },

        data() {
            return {
                values: {
                    username: '',
                    usersurname: '',
                    email: '',
                    phone: '',
                    datebirth: '',
                    password: '',
                },
                errors: {
                    non_field_errors: null,
                    username: null,
                    usersurname: '',
                    email: null,
                    phone: '',
                    datebirth: '',
                    password: null,
                },
                isSubmitted: false,
            };
        },

        methods: {
            async onFormSubmit(e) {
                let data = new FormData(e.target);

                try {
                    await this.$axios.post(this.$api.auth.reg, data);
                    this.isSubmitted = true;
                    this.$emit('success');
                } catch (e) {
                    this.errors = {...this.errors, ...e.response.data};
                    this.$emit('error', this.errors);
                    this.errors.non_field_errors = null;
                }
            },
        },

    };
</script>

<style lang="scss" module>
    .registration {
        position: relative;
        width: 100%;
        height: 100vh;
    }

    .bgLeft {
        position: fixed;
        width: 50%;
        height: 100%;
        top: 0;
        left: 0;
        background: $white;

        @include _1023 {
            display: none;
        }
    }

    .bgRight {
        position: fixed;
        width: 50%;
        height: 100%;
        top: 0;
        right: 0;
        background: $grey-xs;

        @include _1023 {
            width: 100%;
        }
    }

    .header {
        display: flex;
        justify-content: space-between;

        @include _1023 {
            height: 120px;
            background: $white;
            padding: 0 40px;
            width: calc(100% + 80px);
            margin: 0 0 0 -40px;
        }

        @include _500 {
            height: 64px;
            padding: 0 24px;
            width: calc(100% + 48px);
            margin: 0 0 0 -24px;
        }
    }

    .main {
        display: flex;

        @include _1023 {
            flex-wrap: wrap;
            padding: 0 40px;
        }

        @include _500 {
            padding: 0;
        }
    }

    .mainLeft {
        width: 50%;
        padding: 0 20px 0 70px;

        @include _1023 {
            width: 100%;
            order: 1;
            background: $white;
            margin: 40px 0 0 0;
            padding: 80px;
            border-radius: 8px;
        }
    }

    .mainRight {
        width: 50%;
        padding: 0 0 0 80px;

        @include _1023 {
            width: 100%;
            padding: 0;
        }
    }

    .title {
        display: inline-block;
        color: $grey-dark;
        max-width: 400px;
        margin: 80px 0 0 0;

        @include _1023 {
            margin: 40px 0 0 0;
        }

        @include _500 {
            margin: 24px 0 0 0;
            max-width: none;
        }
    }

    .subtitle {
        display: inline-block;
        max-width: 500px;
        margin: 16px 0 0 0;
    }

    .social {
        margin: 40px 0 0 0;
    }

    .registrationForm {
        max-width: 448px;
    }

    .input {
        padding: 0 0 16px 0;
    }
</style>
