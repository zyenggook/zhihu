<template>
  <div>
    <a v-if="display">
      <button
        v-if="isSubscribedTo"
        @click="toggleSubscription"
        class="btn btn-outline-secondary btn-sm mr-2"
      >
        <i class="fa fa-eye-slash"></i> 取消关注
      </button>
      <button
        v-else
        @click="toggleSubscription"
        class="btn btn-primary btn-sm mr-2"
      >
        <i class="fa fa-eye"></i> 关注问题
      </button>
    </a>

    <a class="text-secondary">
      <i class="fa fa-folder"></i>
      <span v-text="this.question.category.name"></span>
    </a>

    <a class="text-secondary">
      <i class="fa fa-eye"></i>
      <span v-text="subscriptionsCount"></span> 人关注
    </a>

    <span> • </span>

    <a class="text-secondary">
      <i class="fa fa-tags"></i>
      <span v-text="answersCount"></span> 个回答
    </a>
    <span> • </span>

    <a
      class="text-secondary"
      role="button"
      href="#"
      data-toggle="modal"
      :data-target="'#' + modalQuestionId"
    >
      <i class="fa fa-comments"></i>
      <span v-text="commentsCount"></span> 个评论
    </a>

    <div
      class="modal fade"
      :id="modalQuestionId"
      tabindex="-1"
      role="dialog"
      :aria-labelledby="modalQuestionId"
      aria-hidden="true"
    >
      <div class="modal-dialog modal-dialog-scrollable">
        <div class="modal-content" style="height: 750px">
          <div class="modal-header">
            <h4 class="modal-title" :id="modalQuestionId">评论列表</h4>
          </div>
          <div class="modal-body">
            <comments
              :data="this.question.comments"
              :subject="this.question"
              @addComment="commentsCount++"
            ></comments>
          </div>
        </div>
        <!-- /.modal-content -->
      </div>
      <!-- /.modal-dialog -->
    </div>
    <span> • </span>
    <a v-if="signedIn" class="text-secondary">
      <button
        type="submit"
        :class="voteUpClasses"
        @click="toggleVoteUp"
        :dusk="`question-up-vote${question.id}`"
        style="background-color: transparent; border-style: none"
      >
        <span></span>
        <span v-text="upVotesCount"></span>
      </button>

      <span> • </span>

      <button
        type="submit"
        :class="voteDownClasses"
        @click="toggleVoteDown"
        :dusk="`question-down-vote${question.id}`"
        style="background-color: transparent; border-style: none"
      >
        <span></span>
        <span v-text="downVotesCount"></span>
      </button>
    </a>
  </div>
</template>
<script>
export default {
  props: ["question", "display"],

  data() {
    return {
      subscriptionsCount: this.question.subscriptionsCount,
      answersCount: this.question.answers_count,
      commentsCount: this.question.commentsCount,
      upVotesCount: this.question.upVotesCount,
      downVotesCount: this.question.downVotesCount,
      isVotedUp: this.question.isVotedUp,
      isVotedDown: this.question.isVotedDown,
      isSubscribedTo: this.question.isSubscribedTo,
    };
  },

  computed: {
    voteUpClasses() {
      return ["fa-thumbs-up", this.isVotedUp ? "fa" : "far"];
    },

    voteDownClasses() {
      return ["fa-thumbs-down", this.isVotedDown ? "fa" : "far"];
    },

    subscriptionClasses() {
      return ["fa-heart", this.isSubscribedTo ? "fa" : "far"];
    },

    voteUpEndpoint() {
      return "/questions/" + this.question.id + "/up-votes";
    },

    voteDownEndpoint() {
      return "/questions/" + this.question.id + "/down-votes";
    },

    subscriptionEndpoint() {
      return "/questions/" + this.question.id + "/subscriptions";
    },

    modalQuestionId() {
      return "modalQuestion" + this.question.id;
    },

    signedIn() {
      return window.App.signedIn;
    },
  },

  methods: {
    toggleVoteUp() {
      if (this.isVotedUp) {
        axios.delete(this.voteUpEndpoint);

        this.isVotedUp = false;
        this.upVotesCount--;

        flash("已取消推荐！");
      } else {
        axios.post(this.voteUpEndpoint);

        this.isVotedUp = true;
        this.upVotesCount++;

        flash("已推荐！");
      }
    },

    toggleVoteDown() {
      if (this.isVotedDown) {
        axios.delete(this.voteDownEndpoint);

        this.isVotedDown = false;
        this.downVotesCount--;

        flash("已取消举报！");
      } else {
        axios.post(this.voteDownEndpoint);

        this.isVotedDown = true;
        this.downVotesCount++;

        flash("已举报！");
      }
    },

    toggleSubscription() {
      if (this.isSubscribedTo) {
        axios.delete(this.subscriptionEndpoint);

        this.isSubscribedTo = false;
        this.subscriptionsCount--;

        flash("已取消关注！");
      } else {
        axios.post(this.subscriptionEndpoint);

        this.isSubscribedTo = true;
        this.subscriptionsCount++;

        flash("已关注！");
      }
    },
  },
};
</script>
