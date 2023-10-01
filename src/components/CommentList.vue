<template>
    <ul class="comment-list">

        <li class="comment-list__item">
            <label class="comment-list__switch-online-label">
                Онлайн комментарии
                <input type="checkbox" class="comment-list__switch-online" v-model="isOnlineMessagesOn" @change="connectToServer">
            </label>
        </li>
        <li class="comment-list__item">
            <button class="comment-list__button-show-form button-show-form" @click="showForm(null)"><a class="form-tab-link" href="#comment-form" tabindex="-1">Оставить комментарий</a></button>
        </li>
        <li class="comment-list__item">
            <p class="comment-list__items">Кол-во комментариев: {{ allComments.length }}</p>
        </li>
        <li class="comment-list__item">
            <comment-form v-show="isFormVisible" :parent="parentId"></comment-form>
        </li>
        <comment class="comment-list__comment" v-for="comment in parentComments" :key="comment.id" :comment="comment" :answers="commentsAnswers" :answers_reaction_sum="commentsReactionSum" @showForm="showForm"></comment>
       
    </ul>
</template>



<script>
import Comment from './Comment.vue';
import CommentForm from './CommentForm.vue';

export default {
    components: {
        Comment,
        CommentForm,
    },

    data() {
        return {
            allComments: [],
            commentsIds: [],
            validComments: [], //Если в массиве есть комментарии, у которых есть parentId, но элемента с таким же id нет в массиве, его показывать не нужно
            parentComments: [],
            commentsAnswers: {}, // Объект, в котором ключами являются id комментариев, а значениями массивы с объектами ответов
            commentsReactionSum: {},
            isFormVisible: 0,
            parentId: null,
            isOnlineMessagesOn: true,
            source: null,
        }
    },

    methods: {
        async getComments() {
            const response = await fetch('http://194.67.93.117:80/comments');
            this.allComments = await response.json();  
            this.allComments.reverse()
        },

        getCommentsIds() {
            this.allComments.forEach(comment => {
               this.commentsIds.push(comment.id) 
            });
        },

        validateComments() {
            this.allComments.forEach(comment => {
                if (this.commentsIds.includes(comment.parentId) || comment.parentId == null) {
                    this.validComments.push(comment)
                }
            }); 
        },

        getCommentsAnswers() {
            this.validComments.forEach(validComment => {
                if (!this.commentsAnswers[validComment.parentId] && validComment.parentId != null) {
                    this.commentsAnswers[validComment.parentId] = []
                }
                if (validComment.parentId) {
                    this.commentsAnswers[validComment.parentId].push(validComment)
                }
            });
        },

        getParentComments() {
            this.allComments.forEach(comment => {
                if (comment.parentId == null) {
                    this.parentComments.push(comment)
                }
            });
        },

        getReactionSum() {
            for (let key in this.commentsAnswers) {
                this.commentsReactionSum[key] = 0
                this.commentsAnswers[key].forEach(answer => {
                    this.commentsReactionSum[key] = this.commentsReactionSum[key] +  Number(answer.reaction)
                });
            }
        },

        showForm(id) {
                this.parentId = id
                this.isFormVisible = 1
        },

        connectToServer() {
            if (this.isOnlineMessagesOn) {
                    this.source = new EventSource('http://194.67.93.117:80/comments/stream')
                    this.source.addEventListener('message', (message) =>{
                        this.allComments.push(JSON.parse(message.data))
                        this.structureInfo()
                })
            } else {
                this.source.close()
            }
        },

        structureInfo() {
            this.commentsIds = []
            this.validComments = []
            this.parentComments = []
            this.commentsAnswers = {}
            this.commentsReactionSum = {}
            this.getCommentsIds()
            this.validateComments()
            this.getParentComments()
            this.getCommentsAnswers()
            this.getReactionSum()
        }


        
    },

    async created() {
        await this.getComments()
        this.structureInfo()
        this.connectToServer()
    }
}
</script>



<style lang="scss">
    .comment-list {
        padding: 0;

    &__items {
        margin-top: 10px;
    }
    // .comment-list__comment

    &__comment {
        
    }
    }


</style>