<template>
  <div id="app">
    <h3>Income</h3>
    <p><strong>Today:</strong> ${{ dailyIncome }}</p>
    <p><strong>This month:</strong> ${{ monthlyIncome }}</p>
	<p><strong>Daily mean:</strong> ${{ dailyMean }}</p>
    <hr>
    <h3>First Active Review</h3>
    <div v-for="review in allReviews">
      <p><strong>Project name:</strong> {{ review.project.name }}</p>
      <p><strong>Submission ID:</strong> {{ review.id }}</p>
      <p><strong>Assigned at:</strong> {{ (new Date(review.assigned_at)).toLocaleTimeString() }}</p>
      <p><strong>Price:</strong> ${{ review.price }}</p>
      <p><a :href="`https://review.udacity.com/#!/submissions/${review.id}`" target="_blank">Resume review</a></p>
    </div>
    <div v-if="!activeReview">
      <p>No currently assigned reviews.</p>
    </div>
  </div>
</template>

<script>
export default {
  name: 'app',
  data: () => ({
    activeReview: null,
    dailyIncome: 0,
    monthlyIncome: 0,
	dailyMean: 0
  }),
  methods: {
    load() {
      this.runRecurring();
      this.updatePopup();
    },
    runRecurring() {
      chrome.runtime.sendMessage({ func: 'runRecurring' });
      setTimeout(this.runRecurring, 10000);
    },
    updatePopup() {
      chrome.storage.local.get('dailyIncome', (data) => {
        if ('dailyIncome' in data) {
          this.dailyIncome = data.dailyIncome;
        }
      });
	  chrome.storage.local.get('dailyMean', (data) => {
        if ('dailyMean' in data) {
          this.dailyMean = data.dailyMean;
        }
      });
      chrome.storage.local.get('monthlyIncome', (data) => {
        if ('monthlyIncome' in data) {
          this.monthlyIncome = data.monthlyIncome;
        }
      });
      chrome.storage.local.get('activeReview', (data) => {
        if ('activeReview' in data) {
          this.activeReview = data.activeReview;
        }
      });
	  chrome.storage.local.get('allReviews', (data) => {
        if ('allReviews' in data) {
          this.allReviews = data.allReviews;
        }
      });
	  
      setTimeout(this.updatePopup, 100);
    },
  },
  mounted() {
    this.load();
  },
};
</script>

<style lang="scss">
#app {
  background: rgb(242, 242, 242);
  font-size: 16px;
  height: 400px;
  padding: 10px;
  width: 400px;
}
</style>
