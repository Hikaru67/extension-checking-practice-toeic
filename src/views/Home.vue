<template>
  <div class="toeic-check">
    <div class="guide">
      Looking for people who overslept to check in !!?
    </div>
    <div
      class="input-group"
      style="margin-top: 30px;"
    >
      <button
        class="btn"
        @click="beforeSearch"
      >
        <span v-if="loading">Loading...</span>
        <span v-else>Search</span>
      </button>
    </div>
    <div class="mt-3 row">
      <div class="col-6 mb-3">
        <input
          v-model="oldPrice"
          type="text"
          class="form-control"
          placeholder="Old price"
        >
      </div>
      <div class="col-6 mb-3">
        <input
          v-model="newPrice"
          type="text"
          class="form-control"
          placeholder="New price"
        >
      </div>
    </div>
    <div class="row">
      <div class="col-6 mb-3">
        <input
          v-model="money"
          type="text"
          class="form-control"
          placeholder="Money"
        >
      </div>
      <div class="col-6">
        Lời: {{ getInterest  }}
      </div>
    </div>
    <div class="content">
      <p v-if="isMorning">{{ CHECK_IN_TITLE }}</p>
      <p v-else>{{PRACTICE_TITLE}}</p>
      <p>{{content}}</p>
      <p>{{listOverslept}}</p>
      <p>{{timeLeft}}</p>
    </div>
  </div>
</template>
<script>
const CHECK_IN_TITLE = 'Danh sách chưa check in: '
const PRACTICE_TITLE = 'Danh sách chưa nộp bài: '
const CHECK_IN_URL = 'https://www.facebook.com/TOEIClachuyennho'
const PRACTICE_URL = 'https://www.facebook.com/groups/361878885441419'
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
      oldPrice: '',
      newPrice: '',
      money: '',
      interest: '',
      loading: false
    }
  },

  computed: {
    isMorning() {
      return (Date.parse(new Date().toString().replace(/[0-9]{2}:[0-9]{2}:[0-9]{2}/gm, '12:00:00')) - Date.now() > 0) ? 1 : 0
    },
    getTimeAgo() {
      return this.timeAgo(this.getTimeLeft(new Date))
    },
    getMessage() {
      if (this.isMorning) { return CHECK_IN_TITLE + "\n" }
      return PRACTICE_TITLE + "\n"
    },
    getMessageDone() {
      if (this.isMorning) { return "Mọi người đã check in đầy đủ, hẹn gặp lại vào buổi tối (O^O) !!!" }
      return "Mọi người đã nộp bài tập đầy đủ, chúc cả nhà ngủ ngon (O^O)... hoặc là khônggg ?!!"
    },
    timeNow() {
      return new Date();
    },
    getInterest() {
      if (!this.newPrice || !this.oldPrice || !this.money) {
        return 0
      }
      return (this.newPrice / this.oldPrice - 1) * this.money
    }
  },

  mounted() {
    this.setTimeLeft()
  },

  methods: {
    setTimeLeft() {
      setInterval(() => {
        this.timeLeft = this.timeAgo(this.getTimeLeft(new Date))
      }, 1000)
    },
    getTimeLeft(date) {
      if (this.isMorning) { return Date.parse(date.toString().replace(/[0-9]{2}:[0-9]{2}:[0-9]{2}/gm, '10:00:00')) - Date.now() }
      return Date.parse(date.toString().replace(/[0-9]{2}:[0-9]{2}:[0-9]{2}/gm, '23:59:59')) - Date.now();
    },

    timeAgo(difference) {
      var result = '';
      if (!difference) {
        difference = this.getTimeLeft(new Date)
      }
      if (difference < 0) {
        console.log('difference :>> ', difference);
        return 'quá hạn';
      }

      //it has secons
      if ((difference % 1000 * 60) > 0) {
        if (Math.floor(difference / 1000 % 60) > 0) {
          let s = Math.floor(difference / 1000 % 60) == 1 ? '' : 's';
          result = `${Math.floor(difference / 1000 % 60)} giây `;
        }
      }

      //it has minutes
      if ((difference % 1000 * 3600) > 0) {
        if (Math.floor(difference / 1000 / 60 % 60) > 0) {
          let s = Math.floor(difference / 1000 / 60 % 60) == 1 ? '' : 's';
          result = `${Math.floor(difference / 1000 / 60 % 60)} phút${result == '' ? '' : ','} ` + result;
        }
      }

      //it has hours
      if ((difference % 1000 * 3600 * 60) > 0) {
        if (Math.floor(difference / 1000 / 60 / 60 % 24) > 0) {
          let s = Math.floor(difference / 1000 / 60 / 60 % 24) == 1 ? '' : 's';
          result = `${Math.floor(difference / 1000 / 60 / 60 % 24)} tiếng${result == '' ? '' : ','} ` + result;
        }
      }

      //it has days
      if ((difference % 1000 * 3600 * 60 * 24) > 0) {
        if (Math.floor(difference / 1000 / 60 / 60 / 24) > 0) {
          let s = Math.floor(difference / 1000 / 60 / 60 / 24) == 1 ? '' : 's';
          result = `${Math.floor(difference / 1000 / 60 / 60 / 24)} ngày${result == '' ? '' : ','} ` + result;
        }
      }

      return result + 'đếm ngược';
    },

    sleep(ms) {
      return new Promise(resolve => setTimeout(resolve, ms));
    },

    async beforeSearch() {
      this.loading = true;
      if (!this.isMorning) {
        if (new Date().toString().search('Sat') > -1 || new Date().toString().search('Sun') > -1 || new Date().toString().search('Thu') > -1) {
          alert("Hôm nay không có bài tập, mọi người buổi tối vui vẻ (O^O)... hoặc là khônggg ?!!")
          this.loading = false;
          return 0
        }
      }
      const [tab] = await chrome.tabs.query({ active: true, currentWindow: true });
      const tabId = tab.id;
      const type = this.isMorning
      const checkInUrl = this.CHECK_IN_URL
      const praticeUrl = this.PRACTICE_URL
      let res;
      res = await chrome.scripting.executeScript({
        target: { tabId: tabId },
        args: [type, checkInUrl, praticeUrl],
        func: (type, checkInUrl, praticeUrl) => {
          console.log(123)
        }
      });
      console.log('res :>> ', res);
      if (res[0].result) {
        await this.sleep(2000)
      } else {
        await this.sleep(6000)
      }
      this.search()
    },

    async search() {
      this.loading = true;
      const [tab] = await chrome.tabs.query({ active: true, currentWindow: true });
      const tabId = tab.id;
      var message = this.getMessage
      var messageDone = this.getMessageDone
      var timeAgo = this.timeAgo(this.getTimeLeft(new Date))
      var data = new Array
      setInterval(() => {
        timeAgo = this.timeAgo(this.getTimeLeft(new Date))
      }, 1000)
      try {
        var res = await chrome.scripting.executeScript({
          target: { tabId: tabId },
          args: [message, messageDone, timeAgo, data],
          func: (message, messageDone, timeAgo, data) => {
            setTimeout(function () {
              var index = 1
              while (index++ < 100000) {
                expand = document.querySelector('.j83agx80.fv0vnmcu.hpfvmrgz')
                console.log('🚀 ~ expand', expand)
                if (expand) {
                  expand.click()
                  break;
                }
              }
            }, 5000)
            console.log(timeAgo);
            new Promise(resolve => setTimeout(resolve, 1500)).then(() => {
              const participates = [
                { name: 'Quang Nguyễn', checked: false },
                { name: 'Trần Vân', checked: false },
                { name: 'Đoàn An Nhiên', checked: false },
                { name: 'Hùng Đỗvăn', checked: false },
                { name: 'Trần Ngọc Ánh', checked: false },
                { name: 'Hoàng Phương Thảo', checked: false },
                { name: 'Trung Thong', checked: false }
              ]
              listComments = document.querySelector('.cwj9ozl2.tvmbv18p')
              comments = listComments.querySelectorAll('ul li .ni8dbmo4.stjgntxs.l9j0dhe7 span.nc684nl6')
              i = 1;
              names = new Array();
              while (i < comments.length) {
                names.push(comments[i].textContent);
                i += 2;
              }

              participates.forEach(item => {
                names.forEach(name => {
                  if ((name === item.name) || (name === "Nguyễn Thanh Xuân" && item.name === "Liinh Mon")) {
                    item.checked = true;
                    return;
                  }
                });
              })

              var list = "";
              participates.forEach(item => {
                if (!item.checked) {
                  console.warn("=>> ", item.name);
                  list += "@" + item.name + "\n";
                  data.push(item.name)
                }
              });
              if (!list) {
                alert(messageDone)
                return messageDone;
              } else {
                alert(message + "\n" + list + "\n" + timeAgo);
                return list;
              }
            });
            console.log('data :>> ', data);
            console.log(window.btoa(data));
            return window.btoa(data);
          }
        }, (result) => {
          console.log('🚀 ~ result', result);
        });
        console.log('res :>> ', res[0].result);
        this.loading = false;
      } catch (e) {
        this.loading = false;
      }
    }
  }
}
</script>