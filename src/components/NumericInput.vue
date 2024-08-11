<template>
    <input class="resizable-input" @beforeinput="(e: Event) => onBeforeinput(e as InputEvent)" @paste="onPaste"
        :value="number" @input="onInputEventHandler" name="number-nikita" id="number-nikita" />
</template>

<script setup lang="ts">
import { ref, watch } from 'vue'

const props = defineProps({
    modelValue: {
        required: false,
        type: String
    }
})

const emit = defineEmits(['update:modelValue'])
const number = ref()

const regexDigits = /^[0-9\s]+$/;

const width = ref('72px')
const minWidth = ref('72px')

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
    width.value = `${number.value.length}ch`
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
.resizable-input {
    width: v-bind(width);
    min-width: v-bind(minWidth);
}

.resizable-input::-webkit-outer-spin-button,
.resizable-input::-webkit-inner-spin-button {
    -webkit-appearance: none;
    margin: 0;
}

.resizable-input[type=number] {
    -moz-appearance: textfield;
}
</style>