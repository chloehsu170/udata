<template>
<div class="row">
    <div class="col-xs-12">
        <box>
            <harvest-form source="{{source}}"></harvest-form>
        </box>
    </div>
</div>
<div class="row">
    <div class="col-xs-12">
        <box title="{{ _('Filters') }}">
            <mappings-form source="{{source}}"></mappings-form>
        </box>
    </div>
</div>
<div class="row">
    <preview class="col-xs-12" source="{{source}}"></preview>
</div>
</template>

<script>
import HarvestSource from 'models/harvest/source';
import ItemModal from 'components/harvest/item.vue';
import Vue from 'vue';

export default {
    name: 'HarvesterEditView',
    props: ['source'],
    data: function() {
        return {
            source: new HarvestSource(),
            source_id: null,
            meta: {
                title: null,
                subtitle: this._('Edit')
            }
        };
    },
    components: {
        'box': require('components/containers/box.vue'),
        'harvest-form': require('components/harvest/form.vue'),
        'mappings-form': require('components/harvest/mappings-form.vue'),
        'preview': require('components/harvest/preview.vue'),
    },
    events: {
        'harvest:job:item:selected': function(item) {
            this.$root.$modal(
                {data: {item: item}},
                Vue.extend(ItemModal)
            );
        }
    },
    watch: {
        source_id: function(id) {
            if (id) {
                this.source.fetch(id);
            }
        },
        'source.name': function(name) {
            if (name) {
                this.meta.title = name;
                this.$dispatch('meta:updated', this.meta);
            }
        }
    }
};
</script>