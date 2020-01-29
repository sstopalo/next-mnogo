<template>
    <div :class="[$style.uiInput, classes]" class="text text__lg">
        <div :class="$style.original">
            <input v-bind="$attrs"
                   :value="value"
                   :type="nativeType"
                   :class="$style.input"
                   class="text text__lg text__lg_bold"
                   :maxlength="calcMaxlength"
                   :disabled="disabled"
                   :autocomplete="autocomplete"
                   @input="onInput"
                   @change="onChange"
                   @focus="onFocus"
                   @blur="onBlur">

            <span v-if="prefixIcon"
                  :class="$style.prefix">
                <i :class="prefixIcon"></i>
            </span>

            <span v-if="isSuffixVisible"
                  :class="$style.suffix">

                <template v-if="!showClear || !showPassword">
                    <i v-if="suffixIcon"
                       :class="suffixIcon">
                    </i>
                </template>

                <i v-if="showClear"
                   :class="$style.icon"
                   class="icon-trash"
                   @click="clear">
                </i>

                <i v-if="showPassword"
                   :class="$style.icon"
                   class="icon-eye"
                   @click="togglePasswordVisibility">
                </i>
            </span>
        </div>

        <span :class="$style.placeholder" class="text text__lg">
            {{ placeholder }}
        </span>
    </div>
</template>

<script>
    const tokens = {
        '#': {pattern: /\d/},
        'S': {pattern: /[a-zA-Z]/},
        'A': {pattern: /[0-9a-zA-Z]/},
        'U': {pattern: /[a-zA-Z]/, transform: v => v.toLocaleUpperCase()},
        'L': {pattern: /[a-zA-Z]/, transform: v => v.toLocaleLowerCase()},
    };

    export default {
        props: {
            type: {
                type: String,
                default: 'text',
            },
            value: {
                type: [String, Number],
                required: true,
            },
            placeholder: String,
            maxlength: [String, Number],
            disabled: Boolean,
            clearable: {
                type: Boolean,
                default: false,
            },
            showPassword: {
                type: Boolean,
                default: false,
            },
            suffixIcon: String,
            prefixIcon: String,
            mask: String,
            autocomplete: {
                type: [String, Array],
                default: 'on',
            },
            error: Boolean,
            success: Boolean,
            warning: Boolean,
            loading: Boolean,
        },

        data() {
            return {
                isFocused: false,
                isPasswordVisible: false,
            };
        },

        computed: {
            classes() {
                return [{
                    [this.$style['_active']]: this.value,
                    [this.$style['_focus']]: this.isFocused,
                    [this.$style['_disabled']]: this.disabled,
                    [this.$style['_prefix']]: this.prefixIcon,
                    [this.$style['_suffix']]: this.suffixIcon || this.clearable || this.showPassword,
                    [this.$style['_error']]: this.error,
                    [this.$style['_success']]: this.success,
                    [this.$style['_warning']]: this.warning,
                    [this.$style['_loading']]: this.loading,
                }];
            },

            showClear() {
                return this.clearable && !this.disabled && this.value;
            },

            isSuffixVisible() {
                return this.suffixIcon || this.showClear || this.showPassword;
            },

            nativeType() {
                if (this.mask || (this.type === 'password' && this.isPasswordVisible)) {
                    return 'text';
                } else {
                    return this.type;
                }
            },

            currentMask() {
                if (this.mask === 'phone') {
                    return '+# (###) ###-##-##';
                } else if (this.mask === 'date') {
                    return '##/##/####';
                } else if (this.mask === 'time') {
                    return '##:##';
                } else {
                    return this.mask;
                }
            },

            calcMaxlength() {
                if (this.mask === 'phone' || this.mask === 'date' || this.mask === 'time') {
                    return this.currentMask.length;
                } else {
                    return this.maxlength;
                }
            },
        },

        created() {
            if (this.mask) {
                this.$emit('input', this.applyMask(this.value));
            }
        },

        methods: {
            onInput(e) {
                if (this.currentMask) {
                    this.$emit('input', this.maskValue(e.target));
                } else {
                    this.$emit('input', e.target.value);
                }
            },

            onChange(e) {
                this.$emit('change', e);
            },

            onFocus(e) {
                this.isFocused = true;
                this.$emit('focus', e);

                //Automatically set '+' character
                if (this.mask === 'phone' && !this.value) {
                    this.$emit('input', '+');
                }
            },

            onBlur(e) {
                this.isFocused = false;
                this.$emit('blur', e);

                //Automatically remove '+' character
                if (this.mask === 'phone' && this.value === '+') {
                    this.$emit('input', '');
                }
            },

            clear() {
                this.$emit('input', '');
                this.$emit('change', '');
                this.$emit('clear');
            },

            togglePasswordVisibility() {
                this.isPasswordVisible = !this.isPasswordVisible;
            },

            maskValue(el) {
                let position = el.selectionEnd;
                let digit = el.value[position - 1];
                let maskedValue = el.value = this.applyMask(el.value);

                while (position < maskedValue.length && maskedValue.charAt(position - 1) !== digit) {
                    position++;
                }
                if (el === document.activeElement) {
                    el.setSelectionRange(position, position);
                    setTimeout(() => {
                        el.setSelectionRange(position, position);
                    }, 0);
                }
                return maskedValue;
            },

            applyMask(value) {
                let iMask = 0;
                let iValue = 0;
                let output = '';
                while (iMask < this.currentMask.length && iValue < value.length) {
                    let cMask = this.currentMask[iMask];
                    let masker = tokens[cMask];
                    let cValue = value[iValue];
                    if (masker) {
                        if (masker.pattern.test(cValue)) {
                            output += masker.transform ? masker.transform(cValue) : cValue;
                            iMask++;
                        }
                        iValue++;
                    } else {
                        output += cMask;
                        if (cValue === cMask) iValue++;
                        iMask++;
                    }
                }

                //Remove mask characters while backspase
                while (output[output.length - 1] === this.currentMask[output.length - 1] && output.length) {
                    //Don't remove '+' character for phone mask
                    if (this.mask === 'phone' && output.length === 1 && output[0] === '+') break;
                    output = output.slice(0, -1);
                }

                return output;
            },
        },
    };
</script>

<style lang="scss" module>
    .uiInput {
        position: relative;
        width: 100%;

        &._active {

            .placeholder {
                left: 13px;
                transform: translateY(2px) scale(.76);
            }
        }

        &._focus {

            .original {
                border: 1px solid $grey-l;
                border-radius: 4px;
            }

            .placeholder {
                left: 13px;
                transform: translateY(2px) scale(.76);
            }
        }

        &._disabled {

            .original {
                border: 1px solid $grey-xs;
                border-radius: 4px;
            }

            .input {
                background-color: $grey-xs;
            }
        }

        &._error {

            .original {
                border: 1px solid $pink;
                border-radius: 4px;
                box-shadow: 0 0 4px rgba(250, 50, 100, .25);
            }
        }

        &._prefix {

            .input {
                padding-left: 32px;
            }

            .placeholder {
                transform: translate(32px, 26px);
            }

            &._active {

                .placeholder {
                    transform: translate(0, 0) scale(.76);
                }
            }
        }

        &._suffix {

            .input {
                padding-right: 32px;
            }
        }

        &._success {

            .input {
                border-color: $green;
            }

            .original:after {
                background-color: $green;
            }

            .placeholder {
                color: $green;
            }
        }

        &._warning {

            .input {
                border-color: $yellow;
            }

            .original:after {
                background-color: $yellow;
            }

            .placeholder {
                color: $yellow;
            }
        }

        &:hover {

            .original {
                border: 1px solid $grey-l;
                border-radius: 4px;
            }
        }
    }

    .original {
        position: relative;
        z-index: 1;
        width: 100%;
        height: 100%;
        border: 1px solid $grey-s;
        border-radius: 4px;
        transition: all .3s ease;
    }

    .input {
        width: 100%;
        padding: 16px;
        background-color: transparent;
        color: $grey-dark;
    }

    .placeholder {
        position: absolute;
        top: 0;
        left: 16px;
        transform: translateY(18px);
        transition: transform .3s ease, color .2s ease;
        pointer-events: none;
        white-space: nowrap;
    }

    .suffix {
        position: absolute;
        height: 100%;
        right: 6px;
        top: 0;
        text-align: center;
        color: $grey-l;

        &:after {
            content: '';
            height: 100%;
            width: 0;
            display: inline-block;
            vertical-align: middle;
        }
    }

    .prefix {
        position: absolute;
        height: 100%;
        left: 6px;
        top: 0;
        text-align: center;
        color: $grey-l;

        &:after {
            content: '';
            height: 100%;
            width: 0;
            display: inline-block;
            vertical-align: middle;
        }
    }

    .icon {
        pointer-events: all;
        transition: color .2s;
        cursor: pointer;

        &:hover {
            color: $grey-xl;
        }
    }
</style>
