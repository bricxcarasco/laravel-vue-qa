<template>
    <div class="media post">
        <div class="d-flex flex-column counters">
            <div class="vote">
                <strong>{{ question.vote_count }}</strong> {{ str_plural('vote', question.vote_count) }}
            </div>
            <div :class="statusClasses">
                <strong>{{ question.answer_count }}</strong> {{ str_plural('answer', question.answer_count) }}
            </div>
            <div class="view">
                {{ question.views + " " + str_plural('view', question.answer_count) }}
            </div>
        </div>
        <div class="media-body">
            <div class="d-flex align-items-center">
                <h3 class="mt-0">
                    <router-link
                        :to="{ name: 'questions.show', params: { slug: question.slug } }"
                    >{{ question.title }}</router-link>
                </h3>
                <div class="ml-auto">        
                    <router-link
                        :to="{ name: 'questions.edit', params: { id: question.id } }"
                        v-if="authorize('modify', question)"
                        class="btn btn-sm btn-outline-info">Edit</router-link>
                
                    <button 
                        v-if="authorize('deleteQuestion', question)"
                        class="btn btn-sm btn-outline-danger" 
                        @click="destroy">Delete</button>
                </div>
            </div>
            <p class="lead">
                Asked by
                <a href="#">{{ question.user.name }}</a>
                <small class="text-muted">{{ question.created_date }}</small>
            </p>
            <div class="excerpt">{{ question.excerpt }}</div>
        </div>
    </div>
</template>
<script>
import destroy from '../mixins/destroy';
import EventBus from '../event-bus';

export default {

    props: ['question'],

    mixins: [destroy],
    
    computed: {
        statusClasses () {
            return [
                'status',
                this.question.status
            ];
        }
    },

    methods: {
        str_plural (str, count) {
            return str + (count > 1 ? 's' : '');
        },

        delete () {
            this.$root.disableInterceptor();
            axios.delete(`/questions/${this.question.id}`)
                .then(({data}) => {
                    this.$toast.success(data.message, "Success", { timeout: 2000 });
                    EventBus.$emit('deleted', this.question.id);
                    this.$root.enableInterceptor();
                })
        }
    }
}
</script>