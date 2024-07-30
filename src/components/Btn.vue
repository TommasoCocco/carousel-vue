<script lang="ts" setup>
import { computed } from 'vue'
const props = defineProps<{
    text?: string
    icon?: string
    modifiers?: string | string[]
}>()

const hasModifiersClasses = computed(() => {
    let normalizedModifiers = typeof props.modifiers === 'string' ?
        props.modifiers?.split(' ') : props.modifiers
    return normalizedModifiers?.map((item) => `btn--${item}`)
})

</script>

<template>
    <button type="button" class="btn" :class="hasModifiersClasses">
        <slot name="icon"><i v-if="icon" class="fa-solid" :class="`fa-${icon}`"></i></slot>
        <slot>{{ text }}</slot>
    </button>
</template>

<style lang="scss" scoped>
.btn {
    height: 50px;
    width: 50px;
    border-radius: 50px;
    border: 0px;
    background-color: #a4a4a4;
    color: white;
    z-index: 100;
    transition: background-color 0.3s ease-in;

    &.btn:hover {
        cursor: pointer;
        background-color: #242424;
    }

    &--dot {
        height: 12px;
        width: 12px;
        background-color: #a4a4a4;
        margin: 0 5px;
        transition: background-color 0.3s ease;
        cursor: pointer;

        &:hover {
            transform: scale(1.2);
            background-color: #313131;
        }

        &.active {
            background-color: #fff;
            border: 1.5px solid #313131;
            height: 15px;
            width: 15px;
        }

    }

}
</style>