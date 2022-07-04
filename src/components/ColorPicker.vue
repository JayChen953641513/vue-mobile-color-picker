<template>
    <input type="text" v-model="value" :style="style" @click="showPicker">
    <div class="pickerContent" v-show="showPickerFlag">
        <div class="btns">
            <div class="cancalBtn" :style="cancalStyle" @click="cancal">{{ cancalTxt }}</div>
            <div class="submitBtn" :style="submitStyle" @click="submit">{{ submitTxt }}</div>
        </div>
        <div class="levelOneSlideContent">
            <div @touchmove="moveLevelOneSlide" class="levelOneSlide"
                :style="`background-color:${colorOne};left:${slideOneLeft}%`"></div>
        </div>
        <div class="levelTwoSlideContent" :style="`background-image:linear-gradient(to right,${colorOne},#000000)`">
            <div class="levelTwoSlide" @touchmove="moveLevelTwoSlide"
                :style="`background-color:${colorTwo};left:${slideTwoLeft}%`"></div>
        </div>
    </div>

</template>

<script setup lang="ts">
import { ref, toRefs } from 'vue'
import { rgbToString, stringToRgb } from './../utils'

const value = ref<string>('')
const slideOneLeft = ref<number>(0)
const colorOne = ref<string>('rgb(255,0,0)')
const slideTwoLeft = ref<number>(0)
const colorTwo = ref<string>(colorOne.value)
const colorThree = ref<string>(colorTwo.value)
const showPickerFlag = ref<boolean>(false)
interface Props {
    cancalTxt: string,
    submitTxt: string,
    cancalStyle?: string,
    submitStyle?: string,
    style?: string,
    valueMode?: string,
    callback: Function
}

const props = withDefaults(defineProps<Props>(), {
    valueMode: 'string',
    cancalTxt: "cancal",
    submitTxt: "submit",
    callback: () => { }
})

const showPicker = () => {
    showPickerFlag.value = true
}
const cancal = () => {
    showPickerFlag.value = false
}
const submit = () => {
    showPickerFlag.value = false
    value.value = colorTwo.value
    props.callback(colorTwo.value)
}
const { valueMode } = toRefs(props)
const moveLevelOneSlide = (e: TouchEvent) => {
    if (e.changedTouches[0].clientX < 0 || e.changedTouches[0].clientX > window.screen.width * 0.95) {
        return false
    }
    const ratio = e.changedTouches[0].clientX / window.screen.width * 100
    const flag = e.changedTouches[0].clientX / window.screen.width * 5
    let result = ``
    if (ratio >= 0 && ratio <= 20) {
        result = `rgb(255,${Math.floor(0xff * flag)},0)`
    } else if (ratio >= 20 && ratio < 40) {
        result = `rgb(${Math.floor(0xff - 0xff * (flag - 1))},255,0)`
    } else if (ratio >= 40 && ratio < 60) {
        result = `rgb(0,255,${Math.floor(0xff * (flag - 2))})`
    } else if (ratio >= 60 && ratio < 80) {
        result = `rgb(0,${Math.floor(0xff - 0xff * (flag - 3))},255)`
    } else if (ratio >= 80 && ratio <= 100) {
        result = `rgb(${Math.floor(0xff * (flag - 4))},0,255)`
    }
    if (valueMode.value === 'string') {
        colorOne.value = rgbToString(result)
        colorTwo.value = rgbToString(result)
    } else {
        colorOne.value = result
        colorTwo.value = result
    }
    slideOneLeft.value = ratio
    slideTwoLeft.value = 0
}
const moveLevelTwoSlide = (e: TouchEvent) => {
    if (e.changedTouches[0].clientX < 0 || e.changedTouches[0].clientX > window.screen.width * 0.95) {
        return false
    }
    const ratio = e.changedTouches[0].clientX / window.screen.width * 100
    const flag = 1 - e.changedTouches[0].clientX / window.screen.width

    const [r, g, b]: any = stringToRgb(colorOne.value, "array")
    const result = `rgb(${Math.floor(r * flag)},${Math.floor(g * flag)},${Math.floor(b * flag)})`
    if (valueMode.value === 'string') {
        colorTwo.value = rgbToString(result)
        colorThree.value = rgbToString(result)
    } else {
        colorTwo.value = result
        colorThree.value = result
    }
    slideTwoLeft.value = ratio
}
</script>

<style lang="less" scoped>
.pickerContent {
    position: fixed;
    width: 100vw;
    bottom: 0;
    left: 0;
    right: 0;
    z-index: 999;
    background: rgba(0, 0, 0, .8);

    .btns {
        margin: 2vw auto;
        display: flex;
        align-items: center;
        justify-content: space-around;

        .cancalBtn,
        .submitBtn {
            background: deepskyblue;
            color: #555;
            border-radius: 5vw;
        }

        div {
            text-align: center;
            width: 40%;
            height: 10vw;
            line-height: 10vw;
        }
    }

    .levelOneSlideContent,
    .levelTwoSlideContent {
        position: relative;
        height: 4vw;
        margin: 8vw auto;
        background-image: linear-gradient(to right, #ff0000, #ffff00, #00ff00, #00ffff, #0000ff, #ff00ff);

        .levelOneSlide,
        .levelTwoSlide {
            position: absolute;
            height: 10vw;
            top: 50%;
            transform: translateY(-50%);
            width: 5vw;
            border-radius: 2px;
            box-shadow: 2px 2px 4px rgba(0, 0, 0, .4), inset 1px 1px 2px #fff;

        }
    }
}
</style>