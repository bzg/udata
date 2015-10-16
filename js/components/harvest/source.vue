<template>
    <datatable title="{{source.name}}" icon="tasks"
        boxclass="harvest-jobs-widget"
        fields="{{ fields }}"
        p="{{ jobs }}"
        empty="{{ _('No job yet') }}">
        <header>
            {{{ source.description | markdown }}}
            <dl class="dl-horizontal">
                <dt>{{ _('Backend') }}</dt>
                <dd>{{ source.backend }}</dd>
                <dt>{{ _('Jobs') }}</dt>
                <dd>{{ jobs.total }}</dd>
            </dl>
        </header>
    </datatable>
</template>

<script>
import {STATUS_CLASSES, STATUS_I18N} from 'models/harvest/job';
import HarvestJobs from 'models/harvest/jobs';

export default {
    name: 'harvest-jobs-widget',
    components: {
        datatable: require('components/datatable/widget.vue')
    },
    data: function() {
        return {
            title: this._('Jobs'),
            source: {},
            jobs: new HarvestJobs({query: {page_size: 10}}),
            fields: [{
                label: this._('Date'),
                key: 'created',
                sort: 'created',
                type: 'datetime'
            }, {
                label: this._('Status'),
                key: 'status',
                type: 'label',
                label_type: function(status) {
                    return STATUS_CLASSES[status];
                },
                label_func: function(status) {
                    return STATUS_I18N[status];
                }
            }]
        };
    },
    events: {
        'datatable:item:click': function(item) {
            this.$dispatch('harvest:job:selected', item);
        }
    },
    props: ['source', 'current'],
    watch: {
        'source.id': function(id) {
            if (id) {
                this.jobs.fetch({ident: id});
            }
        }
    }
};
</script>