<template lang="">
    <li class="comment">
        <div class="comment__content" :class="{comment__content_negative: answers_reaction_sum[comment.id]<0,
                                               comment__content_positive: answers_reaction_sum[comment.id]>0, }">
            <h2 class="comment__author">{{comment.author}}</h2>
            <p class="comment__text">{{comment.text}}</p>
            <div class="comment__bottom-row">
                <time :datetime="comment.createdAt" class="comment__date-time">{{toRightDate(comment.createdAt)}}</time>
                <p class="comment__answers-count" v-if="answers[comment.id]">
                    {{answers[comment.id].length}}
                    <icon-base class="comment__replies-icon">
                            <message-icon />
                    </icon-base> 
                </p>
                <button class="comment__button-show-form" @click="showReplyForm(comment.id)"><a class="form-tab-link" href="#comment-form" tabindex="-1">Ответить</a></button>
            </div>
        </div>
        <ul class="comment__answers" v-if="answers[comment.id]">
            <comment v-for="comment in answers[comment.id]" :comment="comment" :answers="answers" :answers_reaction_sum="answers_reaction_sum" @showForm="showReplyForm"></comment>
        </ul>
    </li>
</template>


<script>
import CommentForm from './CommentForm.vue';
import IconBase from './IconBase.vue';
import MessageIcon from './icons/MessageIcon.vue';
export default {
    components: {
        CommentForm, IconBase, MessageIcon
    },

    

    props: {
    comment: {
            type: Object,
            required: true,
        },
        answers: {
            type: Object,
        },
        answers_reaction_sum: {
            type: Object,
        }
    },

    data() {
        return {
            isFormVisible: 0,
        }
    },

    methods: {
        showReplyForm(id) {
            this.$emit('showForm', id)
            
        },

        toRightDate(date) {
            let dateMilliseconds = +(new Date(date))
            let rightDate = new Intl.DateTimeFormat('ru', {
                year: "numeric", 
                month: "numeric", 
                day: "numeric", 
                hour: "numeric", 
                minute: "numeric",}).format(dateMilliseconds)
            let time = rightDate.slice(12,17)
            let day = rightDate.slice(0,10)
            let dateString = `${time} ${day}`
            return(dateString)
        }
    },
    

   
}
</script>


<style lang="scss">
    @use "../css/scss/vars" as var;
    .comment {
        // .comment_date-time

        &_date-time {}

        // .comment__content

        &__content {
            margin-top: 10px;
            background-color: #fff;
            max-width: 600px;
            padding: 20px;


            @media (min-width: 2100px) {
                max-width: 900px;
            }
            &_negative {
                background-color: rgb(255, 211, 211);
            }

            &_positive {
                background-color: rgb(180, 255, 180);
            }
        }

        // .comment__author

        &__author {
            font-family: var.$main-font;
            font-size: 1.25rem;
            line-height: 1rem;
            font-weight: 400;
            opacity: 0.7;
        }

        // .comment__text

        &__text {
            margin-top: min(2.5vw, 15px);
            font-family: var.$main-font;
            font-size: 1rem;
            line-height: 1rem;
            font-weight: 400;
            word-break: break-word;
            @media (min-width: 2100px) {
                margin-top: 25px;
            }
        }

        // .comment__bottom-row

        &__bottom-row {
            display: flex;
            align-items: center;
            justify-content: flex-end;
            gap: 10px;

            font-family: var.$secondary-font;
            font-size: 1rem;
            line-height: 1rem;
            font-weight: 500;
            
            margin-top: min(2.5vw, 15px);
            @media (min-width: 2100px) {
                margin-top: 25px;
            }
        }



        &__date-time {
            margin-right: auto;
        }


        &__replies-icon {
            width: 1rem;
            height: 1rem;   
        }

        // .comment__button-show-form

        &__button-show-form {
            border: none;
            outline: none;
            background: none;

            padding: 0;
            font-family: var.$secondary-font;
            font-size: 1rem;
            line-height: 1rem;
            font-weight: 500;

            

        }

        // .comment__answers-count

        &__answers-count {
            
        }

        // .comment__answers

        &__answers {
            padding-left: clamp(3px, 1.5vw, 20px);
            border-left: 2px solid rgb(0, 0, 0, 0.3);
        }
        }
    .button-show-form {
    }
    .form-tab-link {
    }


</style>