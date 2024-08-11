<template>
    <input class="resizable-input" @beforeinput="(e: Event) => onBeforeinput(e as InputEvent)" @paste="onPaste"
        :value="number" @input="onInput" name="number-nikita" id="number-nikita" />
</template>

<script setup lang="ts">
import { ref } from 'vue'

const number = ref()
const digits = ['1', '2', '3', '4', '5', '6', '7', '8', '9', '0'] // Можно использовать регулярное выражение, но предпочту так

const width = ref('72px')
const minWidth = ref('72px')

const onPaste = (e: ClipboardEvent) => {
    e.preventDefault()
}

const onBeforeinput = (e: InputEvent) => {
    // нужно сделать обработку для ctr+v
    if (e.data && !digits.includes(e.data)) {
        e.preventDefault()
        return
    }
}

const onInput = (e: Event) => {
    if (!e.target) return

    const el = e.target as HTMLInputElement
    let str = []
    let i = 0;
    const clearedStr = el.value.replace(/ /g, '')

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

    width.value = `${number.value.length}ch`
}

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