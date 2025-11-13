<template>
  <div class="page-container">
    <div class="flex">
      <div class="roulette-wrapper">
        <div ref="rouletteRef" class="roulette"></div>
        <div class="roulette-border">
          <a class="btn-spin" @click="onSpin">SPIN</a>
        </div>
      </div>
      <div class="control-panel">
        <div class="current-selection">
          <div class="label">欲開出的賽果：</div>
          <div class="value">
            {{
              awardList.current
                ? `${awardList.current.name} (${awardList.current.value})`
                : "隨機抽選"
            }}
          </div>
        </div>
        <div class="selector-wrapper">
          <label class="selector-label">控制賽果：</label>
          <select v-model="awardList.current" class="award-selector">
            <option :value="undefined">隨機抽選</option>
            <option
              v-for="award in awardList.list"
              :key="award.value"
              :value="award"
            >
              {{ award.name }} ({{ award.value }})
            </option>
          </select>
        </div>
      </div>
    </div>
    <footer class="page-footer">
      <p class="copyright">© 2025 Wayne. All rights reserved.</p>
      <p class="tech-info">Made with PixiJS, Vue 3 & GSAP</p>
    </footer>
  </div>
</template>

<script setup lang="ts">
import { ref, reactive, onMounted } from "vue"
import {
  Application,
  Container,
  Graphics,
  Sprite,
  Text,
  Texture,
} from "pixi.js"
import { gsap } from "gsap"

const rouletteRef = ref<HTMLElement>()

/** 中間轉盤寬度 */
const ROULETTE_WIDTH = 400
/** 中間轉盤高度 */
const ROULETTE_HEIGHT = 400
/** 轉盤偏移量 */
const OFFSET = 20
/** 轉盤動畫持續時間 */
const ROULETTE_DURATION_SECOND = 5
/** 轉盤額外轉動圈數 */
const ROULETTE_EXTRA_ROUNDS = 10

let app: Application
let rouletteContainer: Container

interface IAward {
  name: string
  value: number
  color: string
  textColor: string
  icon: string
}

const awardList = reactive<{ list: IAward[]; current?: IAward }>({
  list: [
    {
      name: "$88",
      value: 1,
      color: "#fee393",
      textColor: "#ce0207",
      icon: new URL(
        "./common/assets/images/roulette-game/red-pack.svg",
        import.meta.url
      ).href,
    },
    {
      name: "$666",
      value: 2,
      color: "#ce0207",
      textColor: "#fff",
      icon: new URL(
        "./common/assets/images/roulette-game/money.svg",
        import.meta.url
      ).href,
    },
    {
      name: "銘謝惠顧",
      value: 3,
      color: "#fee393",
      textColor: "#ce0207",
      icon: new URL(
        "./common/assets/images/roulette-game/highfive.svg",
        import.meta.url
      ).href,
    },
    {
      name: "$88",
      value: 4,
      color: "#ce0207",
      textColor: "#fff",
      icon: new URL(
        "./common/assets/images/roulette-game/money.svg",
        import.meta.url
      ).href,
    },
    {
      name: "$88",
      value: 5,
      color: "#fee393",
      textColor: "#ce0207",
      icon: new URL(
        "./common/assets/images/roulette-game/red-pack.svg",
        import.meta.url
      ).href,
    },
    {
      name: "$666",
      value: 6,
      color: "#ce0207",
      textColor: "#fff",
      icon: new URL(
        "./common/assets/images/roulette-game/money.svg",
        import.meta.url
      ).href,
    },
    {
      name: "銘謝惠顧",
      value: 7,
      color: "#fee393",
      textColor: "#ce0207",
      icon: new URL(
        "./common/assets/images/roulette-game/highfive.svg",
        import.meta.url
      ).href,
    },
    {
      name: "$666",
      value: 8,
      color: "#ce0207",
      textColor: "#fff",
      icon: new URL(
        "./common/assets/images/roulette-game/money.svg",
        import.meta.url
      ).href,
    },
  ],
  current: undefined,
})

// 載入圖片
function loadImage(url: string): Promise<HTMLImageElement> {
  return new Promise((resolve, reject) => {
    const img = new Image()
    img.src = url
    img.crossOrigin = "anonymous"
    img.onload = () => resolve(img)
    img.onerror = reject
  })
}

async function createRoulette() {
  // 獲取容器的實際寬高
  const containerWidth =
    (rouletteRef.value?.clientWidth || ROULETTE_WIDTH) - OFFSET
  const containerHeight =
    (rouletteRef.value?.clientHeight || ROULETTE_HEIGHT) - OFFSET

  app = new Application()
  await app.init({
    width: containerWidth,
    height: containerHeight,
    backgroundAlpha: 0,
    resolution: window.devicePixelRatio || 1,
    autoDensity: true,
  })
  rouletteRef.value?.appendChild(app.canvas)

  rouletteContainer = new Container()
  rouletteContainer.x = containerWidth / 2
  rouletteContainer.y = containerHeight / 2
  app.stage.addChild(rouletteContainer)

  const segmentCount = awardList.list.length
  const segmentAngle = (2 * Math.PI) / segmentCount
  const radius = containerWidth / 2

  for (let i = 0; i < awardList.list.length; i++) {
    const award = awardList.list[i]
    if (!award) continue

    const startAngle = i * segmentAngle - Math.PI / 2
    const endAngle = startAngle + segmentAngle
    const midAngle = (startAngle + endAngle) / 2

    // 畫扇形區塊
    const g = new Graphics()
    g.beginFill(award.color)
    g.moveTo(0, 0)
    g.arc(0, 0, radius, startAngle, startAngle + segmentAngle)
    g.lineTo(0, 0)
    g.endFill()
    rouletteContainer.addChild(g)

    // 上文字
    const label = new Text(`${award.name}(${award.value})`, {
      fontSize: 16,
      fontWeight: "bold",
      fill: award.textColor,
      align: "center",
    })
    label.anchor.set(0.5)

    // 設定文字位置
    label.x = Math.cos(midAngle) * radius * 0.5
    label.y = Math.sin(midAngle) * radius * 0.5

    // 設定文字角度
    label.rotation = midAngle
    rouletteContainer.addChild(label)

    // 上icon圖
    try {
      const img = await loadImage(award.icon)
      const texture = Texture.from(img)
      const sprite = new Sprite(texture)
      sprite.anchor.set(0.5)
      sprite.scale.set(0.5)

      // 設定icon位置
      sprite.x = Math.cos(midAngle) * radius * 0.8
      sprite.y = Math.sin(midAngle) * radius * 0.8

      // 設定icon角度
      sprite.rotation = midAngle
      rouletteContainer.addChild(sprite)
    } catch (err) {
      console.error("圖片載入失敗：", award.icon, err)
    }
  }
}

onMounted(() => {
  createRoulette()
})

const isSpinning = ref(false)

function onSpin() {
  if (awardList.current && awardList.current.value) {
    handleSpin(awardList.current)
    return
  }

  const targetIndex = Math.floor(Math.random() * awardList.list.length)
  const targetAward = awardList.list[targetIndex]
  if (targetAward) {
    handleSpin(targetAward)
  }
}

function handleSpin(result: (typeof awardList.list)[number]) {
  if (isSpinning.value) return
  isSpinning.value = true

  const segmentCount = awardList.list.length
  const segmentAngle = (2 * Math.PI) / segmentCount
  const targetIndex = awardList.list.findIndex((a) => a.value === result.value)

  // 將目前角度限制在 0 ~ 2PI 範圍內，避免累加導致動畫失效
  const currentRotation = rouletteContainer.rotation % (2 * Math.PI)
  rouletteContainer.rotation = currentRotation // 🔧 重設 rotation 為合理值

  // 目標角度：該區塊的中間對準上方
  const targetAngle = -targetIndex * segmentAngle - segmentAngle / 2

  const totalRotation =
    2 * Math.PI * ROULETTE_EXTRA_ROUNDS + (targetAngle - currentRotation)

  gsap.to(rouletteContainer, {
    rotation: currentRotation + totalRotation,
    duration: ROULETTE_DURATION_SECOND,
    ease: "power4.out",
    onComplete: () => finishSpin(result),
  })
}

function finishSpin(result: (typeof awardList.list)[number]) {
  console.log("抽中的獎項是：", result)
  isSpinning.value = false
}
</script>

<style lang="sass" scoped>
.page-container
  width: 100%
  min-height: 100vh
  display: flex
  flex-direction: column
  align-items: center
  justify-content: center

.flex
  display: flex
  gap: 0.875rem
  padding: 1.25rem
  flex-wrap: wrap
  justify-content: center
  align-items: center
  width: 100%

.roulette-wrapper
  background: transparent
  border-radius: 0.625rem
  display: flex
  flex-direction: column
  width: 26.25rem
  max-width: 100%
  position: relative
  aspect-ratio: 1 / 1

  .roulette-border
    background-image: url("./common/assets/images/roulette-game/border.svg")
    background-size: contain
    background-position: center center
    background-repeat: no-repeat
    width: 100%
    height: 100%
    position: absolute
    top: 50%
    left: 50%
    transform: translate(-50%, -50%)
    z-index: 3

    &:after
      background-image: url("./common/assets/images/roulette-game/arrow.svg")
      background-size: contain
      background-position: center center
      background-repeat: no-repeat
      content: ""
      display: block
      width: 12.4%
      height: 23.1%
      position: absolute
      top: -4.3%
      left: 50%
      transform: translateX(-50%)
      z-index: 5

  .roulette
    position: absolute
    top: 50%
    left: 50%
    transform: translate(-50%, -50%)
    z-index: 2
    width: 100%
    height: 100%

    canvas
      width: 100% !important
      height: 100% !important
      display: block

  .btn-spin
    border-radius: 50em
    background-image: url("./common/assets/images/roulette-game/btn-spin.svg")
    background-size: cover
    background-position: center center
    background-repeat: no-repeat
    display: flex
    align-items: center
    justify-content: center
    width: 17.86%
    height: 17.86%
    min-width: 3rem
    min-height: 3rem
    aspect-ratio: 1 / 1
    padding: 0
    font-size: clamp(0.875rem, 3.3vw, 1.375rem)
    position: absolute
    top: 50%
    left: 50%
    z-index: 10
    transform: translate(-50%, -50%)
    color: #E5C05A
    cursor: pointer
    text-align: center
    font-feature-settings: 'liga' off, 'clig' off
    text-shadow: 0.0625rem 0.125rem 0.125rem rgba(0, 0, 0, 0.40)
    font-family: Roboto
    font-style: normal
    font-weight: 700
    line-height: normal
    text-transform: uppercase
    transition: transform 0.2s ease

    &:hover
      transform: translate(-50%, -50%) scale(1.05)

    &:active
      transform: translate(-50%, -50%) scale(0.95)

.control-panel
  width: 17.5rem
  max-width: 100%
  margin-left: 0
  display: flex
  flex-direction: column
  gap: 1.25rem

  .current-selection
    background: linear-gradient(135deg, #667eea 0%, #764ba2 100%)
    border-radius: 0.75rem
    padding: 1.25rem
    color: white
    box-shadow: 0 0.25rem 0.9375rem rgba(0, 0, 0, 0.2)

    .label
      font-size: 0.875rem
      opacity: 0.9
      margin-bottom: 0.5rem
      font-weight: 500

    .value
      font-size: 1.25rem
      font-weight: bold
      color: #fee393
      text-shadow: 0.0625rem 0.0625rem 0.125rem rgba(0, 0, 0, 0.3)
      min-height: 1.5rem

  .selector-wrapper
    background: white
    border-radius: 0.75rem
    padding: 1.25rem
    box-shadow: 0 0.25rem 0.9375rem rgba(0, 0, 0, 0.1)

    .selector-label
      display: block
      font-size: 0.875rem
      color: #333
      font-weight: 600
      margin-bottom: 0.625rem
      text-align: left

    .award-selector
      width: 100%
      padding: 0.75rem 1rem
      font-size: 1rem
      border: 0.125rem solid #e0e0e0
      border-radius: 0.5rem
      background: white
      cursor: pointer
      transition: all 0.3s ease
      font-family: inherit
      color: #333

      &:hover
        border-color: #667eea

      &:focus
        outline: none
        border-color: #667eea
        box-shadow: 0 0 0 0.1875rem rgba(102, 126, 234, 0.1)

      option
        padding: 0.625rem
        font-size: 1rem

// RWD 響應式設計
@media (max-width: 1024px)
  .roulette-wrapper
    width: 32rem

  .control-panel
    width: 16rem

@media (max-width: 768px)
  .flex
    flex-direction: column
    align-items: center
    padding: 1rem

  .roulette-wrapper
    width: 28rem

  .control-panel
    width: 100%
    max-width: 28rem
    margin-left: 0
    margin-top: 1.25rem

.page-footer
  margin-top: 3rem
  padding: 1.5rem 1rem
  text-align: center
  color: #666
  border-top: 1px solid #e0e0e0

  .copyright
    margin: 0 0 0.5rem 0
    font-size: 0.875rem
    font-weight: 500

  .tech-info
    margin: 0
    font-size: 0.8125rem
    color: #999

@media (max-width: 768px)
  .page-footer
    margin-top: 1rem
    padding: 1rem

    .copyright
      font-size: 0.8125rem

    .tech-info
      font-size: 0.75rem
</style>
