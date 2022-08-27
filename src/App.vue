<template>
    <div class="app">
        <h1>Страница с постами</h1>
        <MyButton style="margin: 15px 0;" @click="showDialog">
            Создать пост
        </MyButton>
        <MyDialog v-model:show="dialogVisible">
            <PostForm @create="createPost"/>
        </MyDialog>
        <PostList v-if="!loading" @remove="removePost" :posts="posts"/>
        <div v-else>Идет загрузка...</div>
    </div>
</template>

<script>
    import PostForm from "@/components/PostForm";
    import PostList from "@/components/PostList";
    import MyDialog from "@/components/UI/MyDialog";
    import MyButton from "@/components/UI/MyButton";
    import axios from 'axios';

    export default {
        name: 'App',
        components: {MyButton, MyDialog, PostForm, PostList},
        data() {
            return {
                posts: [],
                dialogVisible: false,
                loading: false
            }
        },
        methods: {
            createPost(post) {
                this.posts.push(post);
                this.dialogVisible = false;
            },
            removePost(post) {
                this.posts = this.posts.filter(item => item.id !== post.id);
            },
            showDialog() {
                this.dialogVisible = true;
            },
            async fetchPosts() {
                try {
                    this.loading = true;
                    const response = await axios.get('https://jsonplaceholder.typicode.com/posts?_limit=10');
                    this.posts = response.data;
                } catch (e) {
                    alert('Error')
                } finally {
                    this.loading = false;
                }
            }
        },
        mounted() {
            this.fetchPosts();
        }
    }
</script>

<style>
    * {
        margin: 0;
        padding: 0;
        box-sizing: border-box;
    }

    .app {
        padding: 20px;
    }
</style>
