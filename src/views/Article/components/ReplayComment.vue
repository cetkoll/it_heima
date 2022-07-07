<template>
  <div>
    <van-nav-bar :title="title" left-arrow @click-left="$emit('close')" fixed>
      <template #left>
        <van-icon name="cross" />
      </template>
    </van-nav-bar>
    <CommentItem :item="comment" class="comment"></CommentItem>
    <van-cell-group>
      <van-cell title="全部回复" />
    </van-cell-group>
    <CommentItem
      v-for="(item, index) in commentList"
      :key="index"
      :item="item"
    ></CommentItem>
    <div class="comment1">
      <div class="bottom">
        <van-button
          type="primary"
          block
          plain
          round
          @click="isCommentShow = true"
          >评论</van-button
        >
      </div>
    </div>
    <van-popup v-model="isCommentShow" position="bottom"
      ><AddComment
        :target="comment.com_id"
        v-if="isCommentShow"
        :art_id="$route.params.article_id"
        @add-comment="
          commentList.unshift($event),
            (isCommentShow = false),
            comment.reply_count++
        "
      ></AddComment
    ></van-popup>
  </div>
</template>

<script>
import CommentItem from './CommentItem'
import { getCommentList } from '@/api/comment'
import AddComment from './AddComment'
export default {
  props: {
    comment: {
      type: Object,
      default: () => ({})
    }
  },
  created () {
    this.getCommentList()
  },
  data () {
    return {
      limit: 10,
      offset: null,
      commentList: {},
      isCommentShow: false
    }
  },
  methods: {
    async getCommentList () {
      try {
        const res = await getCommentList({ type: 'c', source: this.comment.com_id, offset: this.offset, limit: this.limit })
        this.commentList = res.data.data.results
      } catch (error) {
        console.log(error)
      }
    }
  },
  computed: {
    title () {
      return this.comment.reply_count === 0 ? '暂无回复' : `${this.comment.reply_count}条回复`
    }
  },
  watch: {},
  filters: {},
  components: { CommentItem, AddComment }
}
</script>

<style scoped lang='less'>
.comment {
  margin-top: 92px;
}
.comment1 {
  margin-bottom: 102px;
}
.bottom {
  height: 102px;
  position: fixed;
  bottom: 0;
  width: 750px;
  padding: 10px 30px;
  box-sizing: border-box;
  background-color: #ff69b4;
}
</style>
