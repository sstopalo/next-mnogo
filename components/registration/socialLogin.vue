<template>
    <div>
        <div :class="$style.head">
            <span :class="$style.title" class="text text__md">или зарегистрируйтесь через соц. сети</span>
        </div>
        <ul :class="$style.list">
            <li :class="$style.item">
                <a href="#" :class="[$style.link, $style._vk]"></a>
            </li>
            <li :class="$style.item">
                <a href="#" :class="[$style.link, $style._fb]"></a>
            </li>
            <li :class="$style.item">
                <a href="#" :class="[$style.link, $style._ok]"></a>
            </li>
            <li :class="$style.item">
                <a href="#" :class="[$style.link, $style._goo]"></a>
            </li>
        </ul>
    </div>
</template>


<style lang="scss" module>
    .list {
        display: flex;
        margin: 16px 0 0 0;
    }

    .item {
        width: 48px;
        height: 40px;
        margin: 0 0 0 8px;

        &:first-child {
            margin: 0;
        }
    }

    .link {
        display: block;
        width: 100%;
        height: 100%;
        border-radius: 4px;
        background-position: center;
        background-repeat: no-repeat;
        transition: all .3s ease;

        &._vk {
            background-color: #5181b8;
            background-image: url("~assets/images/vk.svg");

            &:hover {
                background-color: #3e6898;
            }
        }

        &._fb {
            background-color: #4267b2;
            background-image: url("~assets/images/fb.svg");

            &:hover {
                background-color: #34528d;
            }
        }

        &._ok {
            background-color: #ee8208;
            background-image: url("~assets/images/ok.svg");

            &:hover {
                background-color: #bb6606;
            }
        }

        &._goo {
            background-color: #ea4335;
            background-image: url("~assets/images/goo.svg");

            &:hover {
                background-color: #d52516;
            }
        }
    }
</style>
