<script setup lang="ts">
import { ref, computed, onMounted, onUnmounted } from 'vue'
import Btn from './Btn.vue';

const props = withDefaults(defineProps<{
    cards: {
        img?: string
        title?: string
        content?: string
    }[];
}>(), {});

const emit = defineEmits(['prev-slide', 'next-slide'])
const cardsForPage = ref(1)
const currentPage = ref(0)
const slideRight = ref(false)
const slideLeft = ref(false)
const carousEl = ref()
const startX = ref(0)
const deltaX = ref(0)
const isDragging = ref(false)
const dragLimit = 50
const hasExceededDragLimit = ref(false)
const totalDots = computed(() => Math.ceil(props.cards.length / cardsForPage.value))
const isPrevDisabled = computed(() => currentPage.value === 0)
const isNextDisabled = computed(() => currentPage.value >= totalDots.value * cardsForPage.value - cardsForPage.value)

// functioni carosello navigation 
const prevPage = () => {
    if (!isPrevDisabled.value) {
        slideRight.value = false
        slideLeft.value = true
        currentPage.value -= cardsForPage.value
        emit('prev-slide')
    }
}

const nextPage = () => {
    if (!isNextDisabled.value) {
        slideLeft.value = false
        slideRight.value = true
        currentPage.value += cardsForPage.value
        emit('next-slide')
    }
}

//se clicco i dots mi manda alla pagina
const goToPage = (pageIndex: number) => {
    currentPage.value = pageIndex * cardsForPage.value
}

const isActiveDot = (index: number) => {
    return Math.floor(currentPage.value / cardsForPage.value) === index - 1
}

const isVisible = (key: number) => {
    return key >= currentPage.value && key < currentPage.value + cardsForPage.value
}

const updateCardsForPage = () => {
    const width = window.innerWidth
    if (width >= 1100) {
        cardsForPage.value = 3
    } else if (width >= 800) {
        cardsForPage.value = 2
    } else {
        cardsForPage.value = 1
    }
    currentPage.value = Math.min(currentPage.value, (totalDots.value - 1) * cardsForPage.value)
}

onMounted(() => {
    updateCardsForPage()
    window.addEventListener('resize', updateCardsForPage)
});

onUnmounted(() => {
    window.removeEventListener('resize', updateCardsForPage)
})

// Funzioni per lo scorrimento del carosello con mouse e touch
const startDrag = (e: MouseEvent | TouchEvent) => {
    isDragging.value = true
    startX.value = 'touches' in e ? e.touches[0].clientX : e.clientX
    deltaX.value = 0
    slideLeft.value = false
    slideRight.value = false
    hasExceededDragLimit.value = false
}

const onDrag = (e: MouseEvent | TouchEvent) => {
    if (!isDragging.value) return
    const currentDeltaX = ('touches' in e ? e.touches[0].clientX : e.clientX) - startX.value

    // No effetto translate nella prima slide e ultima pagina
    if (currentPage.value === 0 && currentDeltaX > 0 || currentPage.value >= props.cards.length - cardsForPage.value && currentDeltaX < 0) {
        return
    }
    deltaX.value = currentDeltaX

    if (Math.abs(deltaX.value) >= dragLimit && !hasExceededDragLimit.value) {
        const direction = deltaX.value > 0 ? -cardsForPage.value : cardsForPage.value
        const newSlideIndex = Math.min(Math.max(currentPage.value + direction, 0), props.cards.length - 1)// prima non andava perché al posto dell'uno c'era :cardsForPage.value
        if (newSlideIndex !== currentPage.value) {
            currentPage.value = newSlideIndex
            if (direction > 0) {
                slideRight.value = true
            } else {
                slideLeft.value = true
            }
        }
        deltaX.value = 0
        startX.value = 'touches' in e ? e.touches[0].clientX : e.clientX
        hasExceededDragLimit.value = true
        isDragging.value = false
    }
}

const endDrag = () => {
    if (!isDragging.value) {
        return
    }
    isDragging.value = false
    deltaX.value = 0
}

const styleTransition = computed(() => ({ transform: `translate(${deltaX.value}px)` }))


</script>

<template>
    <section class="carousel-wrapper">
        <btn @click="prevPage" class="carousel-wrapper__btn--left" :disabled="isPrevDisabled"
            :class="{ 'disabled': isPrevDisabled }" icon="fa-solid fa-chevron-left">
        </btn>

        <transition-group :name="slideLeft ? 'transition-left' : 'transition-right'" tag="div" class="carousel"
            ref="carousEl" @mousedown="startDrag" @mousemove="onDrag" @mouseup="endDrag" :style="styleTransition"
            @touchstart.passive="startDrag" @touchmove.passive="onDrag" @touchend="endDrag" appear>
            <template v-for="(card, key) in cards" :key="key" class="carousel-slide"
                :class="{ 'slide-left': slideLeft, 'slide-right': slideRight }">
                <div class="card" v-if="isVisible(key)">
                    <img :src="card.img" alt="" class="card__img">
                    <div class="card__box">
                        <h2 class="card__title">{{ card.title }}</h2>
                        <p class="card__content">{{ card.content }}</p>
                    </div>
                </div>
            </template>
        </transition-group>

        <btn @click="nextPage" class="carousel-wrapper__btn--right" :disabled="isNextDisabled"
            :class="{ 'disabled': isNextDisabled }" icon="fa-solid fa-chevron-right">
        </btn>

        <div class="dots">
            <btn modifiers="dot" v-for="index in totalDots" :key="index" :class="{ active: isActiveDot(index) }"
                @click="goToPage(index - 1)">
            </btn>
        </div>
    </section>
</template>

<style lang="scss" scoped>
.carousel-wrapper {
    display: flex;
    justify-content: center;
    align-items: center;
    position: relative;
    gap: 20px;
    padding: 0 1rem;
    min-height: 450px;
}

.carousel {
    display: flex;
    position: relative;
    gap: 25px;
    -webkit-user-select: none;
    -moz-user-select: none;
    -ms-user-select: none;
    user-select: none
}

.card {
    width: 270px;
    min-height: 300px;
    border-radius: 10px;
    cursor: pointer;
    position: relative;
    background: white;

    &__box {
        padding: 1rem 2rem;
    }

    &__title {
        font-size: 30px;
        margin: 0;
    }

    &__img {
        height: 220px;
        width: 100%;
        object-fit: cover;
        pointer-events: none;
        border-top-left-radius: 10px;
        border-top-right-radius: 10px;
    }

    &__content {
        color: gray;
    }
}

.dots {
    position: absolute;
    bottom: 1rem;
    display: flex;
    justify-content: center;
    width: 100%;
    margin-top: 1rem;
    align-items: center;
}

.disabled {
    visibility: hidden;
}

//effetti transition carosello
.transition-right-enter-active,
.transition-left-enter-active {
    transition: all 0.4s cubic-bezier(0.55, 0, 0.6, 1.4)
}

.transition-right-leave-active,
.transition-left-leave-active {
    opacity: 0;
}

.transition-right-enter-from,
.transition-right-leave-to {
    opacity: 0;
    transform: translate(50px, 0);
}

.transition-left-enter-from,
.transition-left-leave-to {
    opacity: 0;
    transform: translate(-50px, 0);
}

@media screen and (max-width: 500px) {
    .carousel-wrapper {
        padding: 0;
    }

    .carousel-wrapper__btn--left,
    .carousel-wrapper__btn--right {
        display: none;
    }
}

@media screen and (max-width: 800px) {
    .dots {
        visibility: hidden;
    }

    .transition-right-enter-from,
    .transition-right-leave-to {
        opacity: 0;
        transform: translate(20px, 0);
    }

    .transition-left-enter-from,
    .transition-left-leave-to {
        opacity: 0;
        transform: translate(-20px, 0);
    }

}
</style>