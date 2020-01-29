<template>
    <div :class="$style.mnogoLogo">
        <a :class="$style.link" href="/">
            <img :class="$style.logo" src="~/assets/images/mnogo-logo.svg">
        </a>
    </div>

</template>

<!--<script>-->

<!--export default {-->
<!--name: 'mnogoLogo',-->
<!--data() {-->
<!--return {-->
<!--src: "https://www.mnogo.ru/images/svg/logo-horizontal.svg",-->
<!--};-->
<!--},-->
<!--};-->
<!--</script>-->

<style lang="scss" module>
    .mnogoLogo {
        display: flex;
        justify-content: space-between;
        align-items: center;
        height: 120px;

        @include _500 {
            height: 64px;
        }
    }

    .link {
        display: inline-block;
        max-width: 136px;
    }

    .logo {
        display: inline-block;
        width: 100%;
        height: auto;

        @include _500 {
            width: 107px;
        }
    }
</style>
