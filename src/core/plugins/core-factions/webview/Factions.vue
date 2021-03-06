<template>
    <div class="factions-wrapper stack">
        <div class="toolbar split space-between">
            <span class="pl-2">{{ faction && faction.name ? faction.name : 'Faction Name Missing' }}</span>
            <Icon class="red--text red--hover hover pr-2" :size="24" icon="icon-times-circle" />
        </div>
        <div class="split" style="width: 100%">
            <div class="factions-nav">
                <Navigation v-bind:pages="pages" v-bind:page="pageIndex" @navigate="setPage" />
            </div>
            <div class="factions-content stack" v-if="faction">
                <component
                    :is="pages[pageIndex].page"
                    class="fade-in"
                    :key="pageIndex"
                    v-bind:faction="faction"
                    v-bind:character="character"
                ></component>
            </div>
        </div>
    </div>
</template>

<script lang="ts">
import { defineComponent } from 'vue';
// Global Components
import Icon from '@components/Icon.vue';
import Button from '@components/Button.vue';
// Local Components
import Navigation from './components/Navigation.vue';
import Header from './components/Header.vue';
import { ExampleFactionData } from './utility/exampleFactionData';
import { FactionPages } from './pages/exports';

export const ComponentName = 'Factions';
export default defineComponent({
    name: ComponentName,
    components: {
        Button,
        Icon,
        Navigation,
        Header,
        ...FactionPages,
    },
    props: {
        emit: Function,
    },
    data() {
        return {
            pageIndex: 0,
            pages: [
                { name: 'Members', page: 'Members' },
                { name: 'Ranks', page: 'Ranks' },
                { name: 'Bank', page: 'Bank' },
                { name: 'Vehicles', page: 'Vehicles' },
                { name: 'Actions', page: 'Actions' },
                { name: 'Settings', page: 'Settings' },
            ],
            faction: null,
            // Character IDs and their associated test ranks...
            // 61a8efe590851930ac59f5ef - 0 Rank // 51a8efe590851930ac59f5eg - 1 Rank // 51a8efe590851930ac59f5cc - 2 Rank
            character: '51a8efe590851930ac59f5eg',
        };
    },
    methods: {
        setPage(pageIndex: number) {
            this.pageIndex = pageIndex;
        },
    },
    mounted() {
        document.addEventListener('keyup', this.handlePress);

        if ('alt' in window) {
            alt.emit(`${ComponentName}:Ready`);
        } else {
            this.faction = ExampleFactionData;
        }
    },
    unmounted() {
        document.removeEventListener('keyup', this.handlePress);
    },
});
</script>

<style>
.toolbar {
    min-height: 35px;
    max-height: 35px;
    background-color: rgba(12, 12, 12, 1);
    border-bottom: 2px solid rgba(28, 28, 28, 1);
    box-shadow: 2px 2px 2px black;
    border-top-right-radius: 6px;
    border-top-left-radius: 6px;
}

.factions-wrapper {
    display: flex;
    min-width: 75vw;
    max-width: 75vw;
    min-height: calc(75vh + 35px);
    max-height: calc(75vh + 35px);
    background-color: rgba(36, 36, 36, 1);
    overflow: hidden;
    border: 2px solid rgba(22, 22, 22, 1);
    box-shadow: 2px 2px 10px black;
    border-top-right-radius: 6px;
    border-top-left-radius: 6px;
}

.factions-nav {
    display: flex;
    flex-direction: column;
    min-width: 175px;
    max-width: 175px;
    height: 100%;
    border-right: 2px solid rgba(28, 28, 28, 1);
    box-sizing: border-box;
}

.factions-content {
    width: 100%;
    height: 100%;
    min-height: 75vh;
    max-height: 75vh;
    box-sizing: border-box;
}
</style>
