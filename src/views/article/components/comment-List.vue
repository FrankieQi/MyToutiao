<template>
    <div class="comment-list">
        <van-cell title="全部评论"></van-cell>
        <van-list
        v-model="loading"
        :finished="finished"
        finished-text="没有更多了"
        @load="onLoad"
        >
        <comment-item
            v-for="(comment,index) in list" 
            :key="index" 
            :comment="comment"
            @reply-click="$emit('reply-click', $event)"
        ></comment-item>
        <!-- <van-cell v-for="(comment,index) in list" :key="index" :title="comment.content" />-->
        </van-list>
        
    </div>
</template>

<script>
import { getComments } from '@/api/comment'
import CommentItem from './comment-item.vue'
export default {
    name: 'CommentList',
    components: {
        CommentItem
    },
    data() {
        return {
            // list: [],
            loading: false,
            finished: false,
            offset: null,
            limit: 10
        }
    },
    props: {
        // 获取文章评论传递文章ID
        // 获取评论回复传评论ID
        source: {
            type: [Number,String,Object],
            required: true
        },
        type: {
            type: String,
            default: 'a'
        },
        list: {
            type: Array,
            default: ()=>[]
        }
    },
    methods: {
        async onLoad() {
        // 异步更新数据
            //1.请求获取数据
            //2.把数据放到列表中
            //3.把本次loading关闭
            //4.判断是否还有数据
            //      如果有，跟新获取下一页的页码
            //      如果没有，则把finished设置为true，不再触发加载更多
            const data  = await getComments({
                news_id: this.source.toString()
            })
            console.log(data)
            this.$emit('update-total-count',data.data.length)
            // this.list.push(data.data)
            const  results  = data.data;
            // console.log(results)
            this.list.push(...results);
            this.loading = false;
            if(results.length >= data.data.length) {
                // this.offset = data.data.last_id;
                this.finished = true;
            } else {
                this.finished = true;
            }
        },
    },
}
</script>

<style lang="less" scoped>

</style>