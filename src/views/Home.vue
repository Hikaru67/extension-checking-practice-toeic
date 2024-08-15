<template>
  <div class="extension">
    <div class="guide">
      Looking for people who overslept to check in !!?
    </div>

    <div
      class="input-group"
      style="margin-top: 30px; margin-bottom: 10px; justify-content: space-between"
    >
      <button
        class="btn"
        @click="buy"
      >
        <span>Buy</span>
      </button>
      <button
        class="btn"
        @click="sell"
      >
        <span>Sell</span>
      </button>
    </div>
    <div
      class="input-group"
      style="margin-top: 30px; margin-bottom: 10px; justify-content: space-between"
    >
      <button
        class="btn"
        @click="doCheckList"
      >
        <span v-if="loading">Loading...</span>
        <span v-else>Do checklist</span>
      </button>
      <button
        class="btn sm"
        @click="doCheckListV2"
      >
        <span v-if="loading">Loading...</span>
        <span v-else>Do checklist V2</span>
      </button>
      
      
    </div>
    <input v-model=branch placeholder="branch" />
    <input v-model=project placeholder="project" />
    <input v-model=params placeholder="params" />
    <p>{{ branchFeature }}</p>
    <p>{{ branchTesting }}</p>
    <p>{{ paramsExtract }}</p>

  </div>
</template>
<script>
const CHECK_IN_TITLE = 'Danh sách chưa check in: '
const PRACTICE_TITLE = 'Danh sách chưa nộp bài: '
const CHECK_IN_URL = 'https://www.facebook.com/TOEIClachuyennho'
const PRACTICE_URL = 'https://www.facebook.com/groups/361878885441419'
const projects = [
  { text: 'new_bag', value: 'new_bag' },
  { text: 'pkteam', value: 'pkteam' },
  { text: 'ecom', value: 'ecom' },
  { text: 'inter', value: 'inter' },
  { text: 'dteam', value: 'dteam' },
]

const PROJECTS = {
  new_bag: {
    git: 'https://git.ghtk.vn/ops/new-bag'
  },
  ecom: {
    git: 'https://git.ghtk.vn/ghtk/ecom'
  },
  pk: {
    git: 'https://git.ghtk.vn/ops/pk-team'
  },
  sce: {
    git: 'https://git.ghtk.vn/linelv/scenario-service'
  },
  pega: {
    git: 'https://git.ghtk.vn/linelv/pegasus'
  },
  ecom_test: {
    git: 'https://git.ghtk.vn/linelv/ecom-unit-testing'
  },
  inter: {
    git: 'https://git.ghtk.vn/ghtk/inter'
  },
  titan: {
    git: 'https://git.ghtk.vn/ops/titan'
  },
  dteam: {
    git: 'https://git.ghtk.vn/ops/dteam'
  },
}
export default {

  data() {
    return {
      content: '',
      listOverslept: [],
      CHECK_IN_TITLE,
      PRACTICE_TITLE,
      CHECK_IN_URL,
      PRACTICE_URL,
      data: '',
      timeLeft: '',
      loading: false,
      branch: '',
      project: '',
      params: '',
    }
  },

  computed: {
    branchFeature() {
      if(!this.branch && !this.project) {
        return ''
      }

      if(!PROJECTS[this.project]) {
        return ''
      }

      return PROJECTS[this.project].git + '/-/merge_requests?scope=all&state=opened&target_branch=feature/' + this.branch
    },

    branchTesting() {
      if(!this.branch && !this.project) {
        return ''
      }

      if(!PROJECTS[this.project]) {
        return ''
      }

      let br = 'testing'
      if (this.project == 'ecom') {
        br = 'testing_env'
      }

      return PROJECTS[this.project].git + `/-/merge_requests?scope=all&state=opened&target_branch=${br}&search=${this.branch.split('-')[1]}`
    },

    paramsExtract() {
      if(!this.params) {
        return ''
      }

      return this.params.split('&').map(i => i.replace('=', ':')).join('\n')
    },
  },

  mounted() {
  },

  methods: {
    sleep(ms) {
      return new Promise(resolve => setTimeout(resolve, ms));
    },

    async buy() {
      try {
        const [tab] = await chrome.tabs.query({ active: true, currentWindow: true });
        const tabId = tab.id;
        this.loading = true;
        var res = await chrome.scripting.executeScript({
          target: { tabId: tabId },
          func: () => {
            document.querySelector('.open-limit-price-input-right-last').click()
            document.querySelector('button.by-button.by-obp__button.by-button--contained.by-button--xx-large.by-button--long').click()
          }
        });
        this.loading = false;
      } catch (e) {
        console.log('e:', e)
        this.loading = false;
      }
    },

    async sell() {
      try {
        const [tab] = await chrome.tabs.query({ active: true, currentWindow: true });
        const tabId = tab.id;
        this.loading = true;
        var res = await chrome.scripting.executeScript({
          target: { tabId: tabId },
          func: () => {
            document.querySelector('.open-limit-price-input-right-last').click()
            document.querySelector('button.by-button.by-obp__button.by-button--contained.by-button--xx-large.by-button--short').click()
          }
        });
        this.loading = false;
      } catch (e) {
        console.log('e:', e)
        this.loading = false;
      }
    },

    async doCheckList() {
      try {
        const [tab] = await chrome.tabs.query({ active: true, currentWindow: true });
        const tabId = tab.id;
        this.loading = true;
        var res = await chrome.scripting.executeScript({
          target: { tabId: tabId },
          func: () => {
            a = document.querySelectorAll('input[type="checkbox"]')
            for (i = 0; i < a.length; i++) {document.querySelectorAll('input[type="checkbox"]')[i].click()}
          }
        });
        this.loading = false;
      } catch (e) {
        console.log('e:', e)
        this.loading = false;
      }
    },

    async doCheckListV2() {
      try {
        const [tab] = await chrome.tabs.query({ active: true, currentWindow: true });
        const tabId = tab.id;
        this.loading = true;
        var res = await chrome.scripting.executeScript({
          target: { tabId: tabId },
          func: () => {
            a = document.querySelectorAll('.o-checkbox.aui-button.aui-button-compact.o-transparent')
            for (i = 0; i < a.length; i++) {document.querySelectorAll('.o-checkbox.aui-button.aui-button-compact.o-transparent')[i].click()}
          }
        });
        this.loading = false;
      } catch (e) {
        console.log('e:', e)
        this.loading = false;
      }
    },
  }
}
</script>