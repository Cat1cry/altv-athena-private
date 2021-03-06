<template>
    <div class="paint-shop-wrapper pl-2">
        <div class="stack">
            <div class="split split-full space-between mt-2">
                <Button
                    class="mr-2"
                    style="width: 100%"
                    :color="pageIndex === 0 ? 'orange' : 'blue'"
                    @click="setPage(0)"
                >
                    {{ locale.PRESETS }}
                </Button>
                <Button
                    class="mr-2"
                    style="width: 100%"
                    :color="pageIndex === 1 ? 'orange' : 'blue'"
                    @click="setPage(1)"
                >
                    {{ locale.CUSTOM }}
                </Button>
            </div>
            <div class="page-filler">
                <component
                    :is="pages[pageIndex]"
                    class="fade-in"
                    :key="pageIndex"
                    v-bind:locale="locale"
                    v-bind:data="data"
                    @set-colour="setColour"
                    @update-finish="updateFinish"
                    @update-pearl="updatePearl"
                ></component>
            </div>
            <div class="split">
                <Button style="width: 100%" color="red" class="mt-4 mr-2" @click="exit">
                    {{ locale.EXIT }}
                </Button>
                <Button style="width: 100%" color="yellow" class="mt-4 mr-2" @click="nextCam">
                    {{ locale.CAMERA }}
                </Button>
                <Button style="width: 100%" color="green" class="mt-4 mr-2" @click="purchase">
                    {{ locale.BUY }}
                </Button>
            </div>
        </div>
    </div>
</template>

<script lang="ts">
import { defineComponent } from 'vue';
import { iPaintshopSync } from '../shared/interfaces';
import { PAINTSHOP_LOCALE } from '../shared/locales';
// Global Components
import Button from '@components/Button.vue';
import Frame from '@components/Frame.vue';
import Icon from '@components/Icon.vue';
import Input from '@components/Input.vue';
import Modal from '@components/Modal.vue';
import Module from '@components/Module.vue';
import RangeInput from '@components/RangeInput.vue';
import Toolbar from '@components/Toolbar.vue';
// Local Components
import Pages from './components/exports';

const ComponentName = 'PaintShop';
export default defineComponent({
    name: ComponentName,
    components: {
        Button,
        Frame,
        Icon,
        Input,
        Modal,
        Module,
        RangeInput,
        Toolbar,
        ...Pages,
    },
    data() {
        return {
            pearl: -1,
            finish1: 12,
            finish2: 12,
            color1: { r: 255, g: 255, b: 255 },
            color2: { r: 255, g: 255, b: 255 },
            pageIndex: 1,
            pages: ['Presets', 'CustomColor'],
            locale: PAINTSHOP_LOCALE,
            data: {
                color: { r: 255, g: 255, b: 255 },
                color2: { r: 255, g: 255, b: 255 },
                finish1: 12,
                finish2: 12,
                pearl: 0,
            },
        };
    },
    mounted() {
        if ('alt' in window) {
            alt.on(`${ComponentName}:Ready`, this.syncData);
            alt.emit(`${ComponentName}:Ready`);
        }
    },
    unmounted() {
        if ('alt' in window) {
            alt.off(`${ComponentName}:Ready`, this.syncData);
        }
    },
    methods: {
        syncData(syncData: iPaintshopSync) {
            this.data = syncData;
        },
        setColour(value: number | { r: number; g: number; b: number; a: number }, isPrimary = true) {
            if (isPrimary) {
                this.color1 = value;
            } else {
                this.color2 = value;
            }

            this.update();
        },
        setPage(index: number) {
            this.pageIndex = index;
        },
        updatePearl(pearl: number) {
            this.pearl = pearl;

            this.update();
        },
        updateFinish(finish1: number, finish2: number) {
            this.finish1 = finish1;
            this.finish2 = finish2;

            this.update();
        },
        update() {
            if ('alt' in window) {
                alt.emit(
                    `${ComponentName}:Update`,
                    this.color1,
                    this.color2,
                    typeof this.color1 === 'number' ? false : true,
                    this.finish1,
                    this.finish2,
                    this.pearl,
                );
            }
        },
        nextCam() {
            if ('alt' in window) {
                alt.emit(`${ComponentName}:NextCam`);
            }
        },
        purchase() {
            if ('alt' in window) {
                alt.emit(
                    `${ComponentName}:Purchase`,
                    this.color1,
                    this.color2,
                    typeof this.color1 === 'number' ? false : true,
                    this.finish1,
                    this.finish2,
                    this.pearl,
                );
            }
        },
        exit() {
            if ('alt' in window) {
                alt.emit(`${ComponentName}:Close`);
            }
        },
    },
});
</script>

<style scoped>
.paint-shop-wrapper {
    position: fixed;
    left: 0vh !important;
    top: 0vh;
    background: rgba(12, 12, 12, 1) !important;
    min-height: 100vh;
    max-height: 100vh;
    min-width: 250px;
    max-width: 250px;
    overflow: hidden;
}

.page-filler {
    overflow: hidden;
    min-height: calc(100vh - 114px);
    max-height: calc(100vh - 114px);
}
</style>
