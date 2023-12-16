<template>
  <div>
    <div id="box" class="bg-blue-300 h-[100px] w-[100px] rounded-full"></div>
    <div
      class="flex flex-col absolute top-0 right-0 p-[10px] bg-gray-200 bg-opacity-30 rounded-bl-[10px] text-white"
    >
      <button @click="onClickPause">{{ pause ? 'Resume' : 'Pause' }}</button>
      <div class="flex gap-x-1 items-center">
        vx
        <input v-model="vx" type="range" max="3000" />
        <input class="bg-black bg-opacity-0 w-20" v-model="vx" type="number" />
      </div>
      <div class="flex gap-x-1 items-center">
        vy
        <input v-model="vy" type="range" max="3000" />
        <input class="bg-black bg-opacity-0 w-20" v-model="vy" type="number" />
      </div>
    </div>
  </div>
</template>

<script setup lang="ts">
  import { gsap } from 'gsap'

  useHead({
    title: '弹球屏保',
  })

  // 生成要移动到的坐标。小球总是会移动到屏幕的某个边上。
  const vx = ref(1000) // 水平速度
  const vy = ref(1000) // 垂直速度
  let positiveX = true
  let positiveY = true
  let x = 0
  let y = 0

  const pause = ref(false)

  function onClickPause() {
    pause.value = !pause.value
    if (!pause.value) update()
  }

  function update() {
    if (pause.value) return

    let frameSec = 0.016
    const _vx = vx.value
    const _vy = vy.value
    const innerWidth = window.innerWidth - 100
    const innerHeight = window.innerHeight - 100
    // 计算新位置
    const nextMoveTime = Math.min(
      (innerWidth - x) / _vx,
      (innerHeight - y) / _vy,
    )

    if (nextMoveTime < frameSec && nextMoveTime > 0) {
      frameSec = nextMoveTime
    }

    x += _vx * frameSec * (positiveX ? 1 : -1)
    y += _vy * frameSec * (positiveY ? 1 : -1)

    if (x > innerWidth) x = innerWidth
    else if (x < 0) x = 0
    if (y > innerHeight) y = innerHeight
    else if (y < 0) y = 0

    // 检查边界
    if (x >= innerWidth) positiveX = false
    else if (x <= 0) positiveX = true
    if (y >= innerHeight) positiveY = false
    else if (y <= 0) positiveY = true

    // 使用 GSAP 更新位置
    gsap.to('#box', { x: x, y: y, duration: frameSec, ease: 'none' })

    requestAnimationFrame(update)
  }

  onMounted(() => {
    update()
    // document.addEventListener(
    //   'click',
    //   () => {
    //     pause = !pause
    //     if (!pause) update()
    //   },
    //   { passive: true },
    // )
    const bgColor = document.body.style.backgroundColor
    document.body.style.backgroundColor = 'black'
    onUnmounted(() => {
      document.body.style.backgroundColor = bgColor
    })
  })
</script>
