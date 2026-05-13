<template>
    <div class="mb-3">
        <v-row v-for="(row, index) in rows" :key="'row_' + index" class="mt-0">
            <v-col>
                <v-item-group class="_btn-group py-0 px-3">
                    <extruder-control-panel-tools-item v-for="macro in row" :key="macro" :name="macro" />
                    <v-tooltip v-if="showDropToolButton && index === rows.length - 1" top>
                        <template #activator="{ on, attrs }">
                            <v-btn
                                dense
                                class="flex-grow-1 px-0 _drop-tool-btn"
                                :disabled="printerIsPrintingOnly"
                                v-bind="attrs"
                                v-on="on"
                                @click="runDropToolMacro">
                                <v-icon small>{{ mdiEject }}</v-icon>
                            </v-btn>
                        </template>
                        <span>{{ dropToolMacroTooltip }}</span>
                    </v-tooltip>
                </v-item-group>
            </v-col>
        </v-row>
    </div>
</template>

<script lang="ts">
import { mdiEject } from '@mdi/js'
import { Component, Mixins } from 'vue-property-decorator'
import BaseMixin from '@/components/mixins/base'
import ControlMixin from '@/components/mixins/control'

@Component({})
export default class ExtruderControlPanel extends Mixins(BaseMixin, ControlMixin) {
    mdiEject = mdiEject

    get rows() {
        const len = this.toolchangeMacros.length
        const cols = Math.ceil(len / Math.ceil(len / 6))
        const rows = []

        for (let i = 0; i < this.toolchangeMacros.length; i += cols) {
            rows.push(this.toolchangeMacros.slice(i, i + cols))
        }

        return rows
    }

    get dropToolMacroName(): string {
        return (this.$store.state.gui.uiSettings.dropToolMacro ?? '').trim()
    }

    get showDropToolButton(): boolean {
        return this.dropToolMacroName.length > 0
    }

    get dropToolMacroTooltip(): string {
        return this.$t('Settings.UiSettingsTab.DropToolTooltip', { macro: this.dropToolMacroName.toUpperCase() })
    }

    runDropToolMacro() {
        this.doSend(this.dropToolMacroName.toUpperCase())
    }
}
</script>

<style scoped>
._btn-group {
    border-radius: 4px;
    display: inline-flex;
    flex-wrap: nowrap;
    max-width: 100%;
    min-width: 100%;

    .v-btn {
        border-radius: 0;
        border-color: rgba(255, 255, 255, 0.12);
        border-style: solid;
        border-width: thin;
        box-shadow: none;
        height: 28px;
        opacity: 0.8;
        min-width: auto !important;
    }

    .v-btn:first-child {
        border-top-left-radius: inherit;
        border-bottom-left-radius: inherit;
    }

    .v-btn:last-child {
        border-top-right-radius: inherit;
        border-bottom-right-radius: inherit;
    }

    .v-btn:not(:first-child) {
        border-left-width: 0;
    }

    .v-btn._drop-tool-btn {
        flex-grow: 0 !important;
        min-width: 40px !important;
    }
}

html.theme--light ._btn-group .v-btn {
    border-color: rgba(0, 0, 0, 0.12);
}
</style>
