<template>
  <div class="p-10">
    <h1>Loading content</h1>
    <ul>
      <li v-for="(topic, index) in topic_list.topics" :key="index+1">
        {{ topic.from }} - {{ topic.to }}: {{ topic.topic }}
      </li>
    </ul>
  </div>
</template>
<script>
import topicList from '~/components/topic.js'

export default {
  data () {
    return {
      topic_list: topicList(this.$route.params.surah)
    }
  },
  created () {
    this.surah_number = this.$route.params.surah
    this.$axios.$get(`${localStorage.base_url}/surah/${this.surah_number}/editions/${localStorage.edition},simple-quran`)
      .then((response) => {
        const translation = response.data[0]
        const arabic = response.data[1]
        for (const [i, ayah] of arabic.ayahs.entries()) {
          translation.ayahs[i].arabic = ayah.text
        }
        translation.topics = this.topic_list.topics
        localStorage[`surah_${this.$route.params.surah}`] = JSON.stringify(translation)
        this.$router.push(`/tadarus/${this.surah_number}`)
      })
  }
}
</script>
