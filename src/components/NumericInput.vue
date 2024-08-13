<template>
    <div :class="{ active: focus }" class="numeric-input">
        <div class="numeric-input__image__wrapper">
            <img v-if="imgSrc" :src="imgSrc" alt="" class="numeric-input__image">
        </div>
        <div class="numeric-input__wrapper">
            <label for="number-nikita" class="numeric-input__label">{{ label }}</label>
            <div class="numeric-input__caption">
                <input ref="inputRef" class="numeric-input__input" @focusin="onFocusin" @focusout="onFocusout"
                    @beforeinput="(e: Event) => onBeforeinput(e as InputEvent)" @paste="onPaste" :value="number"
                    @input="onInputEventHandler" name="number-nikita" id="number-nikita" />
                {{ caption }}
            </div>
        </div>
    </div>
</template>

<script setup lang="ts">
import { computed, ref, watch } from 'vue'

const props = defineProps({
    modelValue: {
        required: false,
        type: String
    },
    imgSrc: {
        required: false,
        type: String
    },
    label: {
        required: true,
        type: String,
        default: 'label'
    },
    caption: {
        required: true,
        type: String,
        default: 'caption'
    }
})

const emit = defineEmits(['update:modelValue'])
const number = ref()
const focus = ref()
const inputRef = ref()
const paddingLeft = ref('8px')
const paddingRight = ref('16px')

const regexDigits = /^[0-9\s]+$/;

const width = ref('72px')
const minWidth = ref('72px')

const onFocusin = () => {
    focus.value = true
}

const onFocusout = () => {
    focus.value = false
}

const onPaste = (e: ClipboardEvent) => {
    e.preventDefault()
}

const onBeforeinput = (e: InputEvent) => {
    // нужно сделать обработку для ctr+v
    if (e.data && !regexDigits.test(e.data)) {
        e.preventDefault()
        return
    }
}

const tempSpan = document.createElement('span');

const resizeInput = () => {
    const input = inputRef.value;
    const style = getComputedStyle(input);
    tempSpan.style.fontFamily = style.fontFamily;
    tempSpan.style.fontSize = style.fontSize;
    tempSpan.style.fontWeight = style.fontWeight;
    tempSpan.style.fontStyle = style.fontStyle;
    tempSpan.style.letterSpacing = style.letterSpacing;
    tempSpan.textContent = '' + number.value;
    document.body.appendChild(tempSpan);
    width.value = `calc(${paddingLeft.value} + ${paddingRight.value} + ${tempSpan.offsetWidth + 2 + 'px'})`;
    document.body.removeChild(tempSpan);
}

function debounce<T extends unknown[]>(fn: (...args: T) => void, delay: number): (...args: T) => void {
    let timeoutId: number | undefined;

    return (...args: T) => {
        if (timeoutId !== undefined) clearTimeout(timeoutId);

        timeoutId = setTimeout(() => {
            fn(...args);
        }, delay);
    };
}

const debouncedResizeInput = debounce(resizeInput, 15)

const onInputHandler = (value: string) => {
    let str = []
    let i = 0;
    const clearedStr = value.replace(/ /g, '')

    while (true) {
        let start = clearedStr.length - (i + 3) < 0 ? 0 : clearedStr.length - (i + 3)
        let end = clearedStr.length - i

        const slice = clearedStr.slice(start, clearedStr.length - i)
        if (end <= 0)
            break;
        str.push(slice)
        i += 3
    }

    number.value = str.reverse().join(" ")

    if (!number.value) {
        width.value = minWidth.value
        return
    }

    emit('update:modelValue', number.value)
    debouncedResizeInput()

}

const onInputEventHandler = (e: Event) => {
    if (!e.target) return

    const el = e.target as HTMLInputElement
    onInputHandler(el.value)
}

watch(() => props.modelValue, () => {
    if (!props.modelValue) return
    if (regexDigits.test(props.modelValue))
        onInputHandler(props.modelValue)
})

</script>

<style scoped>
.numeric-input {
    display: flex;
    gap: 16px;
}

.active .numeric-input__image__wrapper {
    outline: 1px solid var(--primary, #3D06D7);
}

.active .numeric-input__label {
    color: var(--primary, #3D06D7)
}

.active .numeric-input__input {
    outline: 1.5px solid var(--primary-light, #906FEE);
    color: var(--dark, #1E0E4C);
}

.numeric-input__label {
    font-size: 16px;
    line-height: 15px;
    letter-spacing: 0.32px;
    font-family: Koulen;
    color: var(--dark, #1E0E4C);
}

.numeric-input__image__wrapper {
    padding: 4px;
    border-radius: 50%;
}

.numeric-input__image {
    height: 80px;
    width: 80px;
}

.numeric-input__caption {
    color: var(--dark, #1E0E4C);
    display: flex;
    gap: 12px;
    align-items: center;
}

.numeric-input__input {
    font-family: "Inter", sans-serif;
    font-size: 1em;
    width: v-bind(width);
    color: var(--light-grey, #CFCADF);
    min-width: v-bind(minWidth);
    padding: 8px v-bind(paddingRight) 8px v-bind(paddingLeft);
    border-radius: 6px;
    height: 44px;
    outline: 1px solid var(--light-grey, #CFCADF);
    transition: all 0.5s ease;
}

.numeric-input__input::-webkit-outer-spin-button,
.numeric-input__input::-webkit-inner-spin-button {
    -webkit-appearance: none;
    margin: 0;
}

.numeric-input__input[type=number] {
    -moz-appearance: textfield;
}
</style>