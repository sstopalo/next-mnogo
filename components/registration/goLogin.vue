<template>
    <div :class="$style.goLogin">
        <div :class="$style.text" class="text text__lg">Уже зарегистрированы?</div>
        <a :class="$style.link" class="text text__lg text__lg_bold link links_blue" href="#">Войти</a>
    </div>
</template>


<style lang="scss" module>
    .goLogin {
        display: flex;
        justify-content: space-between;
        align-items: center;
    }

    .text {
        line-height: 1em;
        display: inline-block;
        margin: 0 8px 0 0;

        @include _500 {
            display: none;
        }
    }

    .link {
        line-height: 1em;
    }
</style>
