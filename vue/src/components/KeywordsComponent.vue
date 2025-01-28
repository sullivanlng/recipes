<template>
  <div class="keywords" v-if="localKeywords.length > 0">
    <draggable v-model="localKeywords" @end="onUpdateKeywords">
      <span
        :key="k.id"
        v-for="k in localKeywords.slice(0, keyword_splice).filter((kk) => {
          return kk.show || kk.show === undefined
        })"
        class="keywords__item pl-1"
        :class="keywordClass(k)"
      >
        <template v-if="enable_keyword_links">
          <a :href="`${resolveDjangoUrl('view_search')}?keyword=${k.id}`">
            <b-badge pill variant="light" class="font-weight-normal">{{ k.label }}</b-badge>
          </a>
        </template>
        <template v-else>
          <b-badge pill variant="light" class="font-weight-normal">{{ k.label }}</b-badge>
        </template>
      </span>
    </draggable>
  </div>
</template>

<script>
import draggable from "vuedraggable"
import { ResolveUrlMixin, EscapeCSSMixin } from "@/utils/utils"

export default {
  name: "KeywordsComponent",
  mixins: [ResolveUrlMixin, EscapeCSSMixin],
  components: {
    draggable,
  },
  props: {
    recipe: Object,
    limit: Number,
    enable_keyword_links: { type: Boolean, default: true },
  },
  data() {
    return {
      localKeywords: [...this.recipe.keywords],
    }
  },
  computed: {
    keyword_splice() {
      return this.limit || this.localKeywords.length
    },
  },
  methods: {
    keywordClass(k) {
      return this.escapeCSS("_keywordname-" + k.label)
    },
    onUpdateKeywords() {
      this.$emit("update:keywords", this.localKeywords)
    },
  },
  watch: {
    recipe: {
      handler(newRecipe) {
        this.localKeywords = [...newRecipe.keywords]
      },
      deep: true,
      immediate: true,
    },
  },
}
</script>
