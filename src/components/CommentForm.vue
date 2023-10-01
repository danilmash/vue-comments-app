<template lang="">
    <div class="modal-container">
        
        <div class="opacity-mask" @click="hideForm()"></div>
        <form id="comment-form" class="comment-form" @submit.prevent="sendComment()"  @keydown.esc="hideForm">
            <label for="" class="comment-form__label">Github Username</label>
            <input required type="text" class="comment-form__input comment-form__input_author" name="username" v-model="username">
            <label for="" class="comment-form__label">Text</label>
            <textarea type="text" class="comment-form__input comment-form__input_textarea" v-model="text" @input="checkButton"></textarea>
            
            <div class="comment-form__bottom-row">
                <div class="comment-form__reaction-container">
                    <label class="comment-form__reaction-label">
                        <input type="radio" class="comment-form__reaction-input" name="reaction" value="-1" v-model.number="reaction">
                        <icon-base class="comment-form__reaction-icon" icon-name="dislike">
                            <dislike-icon />
                        </icon-base>
                    </label>
                    <label class="comment-form__reaction-label">
                        <input type="radio" class="comment-form__reaction-input" name="reaction" value="0" v-model.number="reaction">
                        <icon-base class="comment-form__reaction-icon" icon-name="neutral">
                            <neutral-icon />
                        </icon-base>
                    </label>
                    <label class="comment-form__reaction-label">
                        <input type="radio" class="comment-form__reaction-input" name="reaction" value="1" v-model.number="reaction">
                        <icon-base class="comment-form__reaction-icon" icon-name="like">
                            <like-icon />
                        </icon-base>
                    </label>
                
                </div>
                <button :disabled="isButtonDisabled" class="comment-form__button">Отправить</button>
            </div>
        </form>
    </div>      
</template>
<script>
import DislikeIcon from './icons/DislikeIcon.vue'
import LikeIcon from './icons/LikeIcon.vue'
import NeutralIcon from './icons/NeutralIcon.vue'
import IconBase from './IconBase.vue'


export default {
    props: {
        parent: {
            type: Number
        }
    },

    components: {
        DislikeIcon, 
        LikeIcon,
        NeutralIcon,
        IconBase,
    },

    data() {
        return {
            username: "",
            text: "",
            reaction: 0,
            isButtonDisabled: 1
        }
    },

    methods: {
        async sendComment() {
            this.isButtonDisabled = 1
            let body = {
                author: this.username,
                text: this.text,
                reaction: this.reaction,
                parentId: this.parent
            }
            let json = JSON.stringify(body)
            let response =  await fetch('http://194.67.93.117:80/comments', {
                method: 'POST',
                headers: {
                    'Content-Type': 'application/json;charset=utf-8',
                    Username: 'danilmash',
                    Accept: 'application/json',
                },
                body: json
            })
           this.hideForm()
           
        },

        checkButton() {
            if (this.text) {
                this.isButtonDisabled= 0
            } else {
                this.isButtonDisabled= 1
            }
        },

        hideForm() {
            this.$parent.isFormVisible = 0
            this.username = ''
            this.text = ''
            this.reaction = 0
        }
    },
}
</script>
<style lang="scss">
.modal-container {
    position: fixed;
    top: 0;
    bottom: 0;
    right: 0;
    left: 0;
    overflow: hidden;
    overflow-y: auto;
    display: flex;
    flex-flow: column nowrap;
    justify-content: center; /* см. ниже */
    align-items: center;
    z-index: 99;
    width: 100%;
    height: 100%;
}
.comment-form {
    position: relative;
    z-index: 2;
    display: flex;
    flex-direction: column;
    width: clamp(270px, 42vw, 500px);

    padding: 20px 30px;

    border: 2px solid #548eaa;
    border-radius: 20px;
    background-color: #c4bfbf;

    // .comment-form__label

    &__label {  
        font-family: "Roboto";
        font-size: 1.2rem;
        font-weight: 500;
        line-height: 1rem;
        margin-top: 10px;
    }

    // .comment-form__input

    &__input {
        margin-top: 10px;
        
        border: none;
        outline: none;
        background: transparent;

        font-family: "Roboto";

        &_author {
            border-bottom: 1px solid #8f8888;
            background: #b9b5b5;

            font-size: 1rem;
            font-weight: 400;
            line-height: 1rem;
            color: #fff;

            &:focus {
                border-bottom: 2px solid #8f8888;
            }
        }

        &_textarea {
            resize: none;

            padding: 10px;

            border: 1px solid #8f8888;
            border-radius: 10px;
            background: #b9b5b5;
            
            font-size: 1rem;
            font-weight: 400;
            line-height: 1rem;
            color: #fff;
            
            height: clamp(70px, 10vw, 200px);

            &:focus {
                border: 2px solid #8f8888;
            }
        }

        
    }

    // .comment-form__bottom-row

    &__bottom-row {
        display: flex;
        justify-content: space-between;

        margin-top: 20px;
    }

    // .comment-form__reaction-container

    &__reaction-container {
    }

    // .comment-form__reaction-label

    &__reaction-label {
    }

    // .comment-form__reaction-input

    &__reaction-input {
        width: 0;

        &:checked {


            &+svg {
                opacity: 1;
            }
        }
        &:focus { 
            

            &+svg {
                border: 1px solid #000;
            }
        }
    }

    // .comment-form__reaction-icon

    &__reaction-icon {
        opacity: 0.4;
        width: 32px;
        height: 32px;
    }

    // .comment-form__button

    &__button {
        cursor: pointer;
        border: 2px solid #548eaa;
        background: #548eaa;
        outline: none;
        color: #fff;
        border-radius: 10px;
        
        padding: 10px 15px;

        &:focus, &:hover {
            outline: none;
            background: #4f5152;
            border-color: #4f5152;
        }

        &:disabled {
            cursor: not-allowed;
            opacity: 0.5;

            &:hover {
                background: #548eaa;
            }
        }
    }
}
.opacity-mask {
    position: absolute;
    z-index: 1;
    background-color: black;
    opacity: 0.5;
    width: 100%;
    height: 100%;
}

</style>