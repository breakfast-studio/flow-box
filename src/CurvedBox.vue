<template>
    <group ref="container">
        <group>
            <mesh @click="emit('clicked')" :scale-y="0.8" @added="boxAdded">
                <boxGeometry :args="[length, 1, 1, segments, 1, 1]" />
                <meshBasicMaterial :color="props.color ?? 'white'" />

                <mesh>
                    <boxGeometry :args="[length, 1, 1, segments, 1, 1]" />
                    <meshBasicMaterial color="black" wireframe />
                </mesh>
            </mesh>
        </group>
    </group>
</template>

<script lang="ts" setup>
import * as THREE from 'three'
import { watch } from 'vue'
import { Flow } from 'three/examples/jsm/modifiers/CurveModifier.js'
import { onBeforeRender, scene } from 'lunchboxjs'

const props = defineProps<{
    color: THREE.ColorRepresentation
    length?: number
    points: [number, number, number][]
    segments?: number
    speed: number
    closed?: boolean
}>()

const length = props.length ?? 3
const segments = props.segments ?? 20

const emit = defineEmits<{
    (event: 'clicked'): void
}>()

watch(
    () => props.color,
    (v) => {
        if (flow) {
            ;(flow.object3D.material as THREE.MeshBasicMaterial).color.set(v)
        }
    }
)

// create curve
const curve = new THREE.CatmullRomCurve3(
    props.points.map((p) => new THREE.Vector3(...p))
)
curve.curveType = 'centripetal'
if (props.closed) {
    curve.closed = true
}

// create geo
let flow: Flow

const boxAdded = ({ instance }: { instance: THREE.Mesh }) => {
    flow = new Flow(instance)
    flow.updateCurve(0, curve)
    scene.value?.add(flow.object3D)
    instance.visible = false
}
onBeforeRender(() => {
    if (flow) {
        flow.moveAlongCurve(0.001 * (props.speed ?? 2))
    }
})
</script>