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
      >Search</button>
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
      timeLeft: ''
    }
  },

  computed: {
    isMorning() {
      return (Date.parse(new Date().toString().replace(/[0-9]{2}:[0-9]{2}:[0-9]{2}/gm, '12:00:00')) - Date.now() > 0) ? 1 : 0
    },
    getTimeAgo() {
      return this.timeAgo()
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
    }
  },

  watch: {
    time(val) {
      console.log('🚀 ~ val', val)
      console.log('object :>> ');
      time = this.getTimeAgo
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
      if (this.isMorning) { return difference = Date.parse(date.toString().replace(/[0-9]{2}:[0-9]{2}:[0-9]{2}/gm, '10:00:00')) - Date.now() }
      return Date.parse(date.toString().replace(/[0-9]{2}:[0-9]{2}:[0-9]{2}/gm, '23:59:59')) - Date.now();
    },
    timeAgo(difference) {
      var result = '';

      if (difference < 5 * 1000) {
        return 'quá hạn';
      } else if (difference < 90 * 1000) {
        return 'phút đếm ngược';
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

    // checkIn() {
    //   expand = document.querySelector('.j83agx80.fv0vnmcu.hpfvmrgz')
    //   if (expand) { expand.click() }
    //   participates = [{ name: 'Quang Nguyễn', checked: false }, { name: 'Trần Vân', checked: false }, { name: 'Anh Phương', checked: false }, { name: 'Liinh Mon', checked: false }, { name: 'Đoàn An Nhiên', checked: false }, { name: 'Hùng Đỗvăn', checked: false }, { name: 'Nguyễn Trang', checked: false }, { name: 'Trần Ngọc Ánh', checked: false }, { name: 'Hoàng Phương Thảo', checked: false }, { name: 'Trung Thong', checked: false }, { name: 'Đoàn An Nhiên', checked: false }];
    //   listComments = document.querySelector('.cwj9ozl2.tvmbv18p')
    //   comments = listComments.querySelectorAll('ul li .ni8dbmo4.stjgntxs.l9j0dhe7 span.nc684nl6')
    //   i = 1;
    //   names = new Array();
    //   while (i < comments.length) {
    //     names.push(comments[i].textContent);
    //     i += 2;
    //   }

    //   participates.forEach(item => {
    //     names.forEach(name => {
    //       if ((name === item.name) || (name === "Nguyễn Thanh Xuân" && item.name === "Liinh Mon")) {
    //         item.checked = true;
    //         return;
    //       }
    //     });
    //   })
    //   this.listOverslept = []
    //   var list = "";
    //   participates.forEach(item => {
    //     if (!item.checked) {
    //       console.warn("=>> ", item.name);
    //       this.listOverslept.push(item.name);
    //       list += "@" + item.name + " ";
    //     }
    //   });
    //   console.log(list + "Deadline chờ đón bạn !!! ;3");
    //   console.warn(timeAgo());
    //   return list;
    // },

    sleep(ms) {
      return new Promise(resolve => setTimeout(resolve, ms));
    },

    async beforeSearch() {
      if (!this.isMorning) {
        if (new Date().toString().search('Sat') > -1 || new Date().toString().search('Sun') > -1 || new Date().toString().search('Thu') > -1) {
          alert("Hôm nay không có bài tập, mọi người buổi tối vui vẻ (O^O)... hoặc là khônggg ?!!")
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
          if (type) {
            document.location = checkInUrl
          } else {
            document.location = praticeUrl
          }
        }
      });
      console.log('res :>> ', res);
      await this.sleep(4000)
      this.search()
    },

    async search() {
      const [tab] = await chrome.tabs.query({ active: true, currentWindow: true });
      const tabId = tab.id;
      var message = this.getMessage
      var messageDone = this.getMessageDone
      var timeAgo = this.getTimeAgo
      try {
        var res = await chrome.scripting.executeScript({
          target: { tabId: tabId },
          args: [message, messageDone, timeAgo],
          func: (message, messageDone, timeAgo) => {
            expand = document.querySelector('.j83agx80.fv0vnmcu.hpfvmrgz')
            if (expand) { expand.click() }
            new Promise(resolve => setTimeout(resolve, 1500)).then(() => {
              const participates = [{ name: 'Quang Nguyễn', checked: false }, { name: 'Trần Vân', checked: false }, { name: 'Anh Phương', checked: false }, { name: 'Liinh Mon', checked: false }, { name: 'Đoàn An Nhiên', checked: false }, { name: 'Hùng Đỗvăn', checked: false }, { name: 'Nguyễn Trang', checked: false }, { name: 'Trần Ngọc Ánh', checked: false }, { name: 'Hoàng Phương Thảo', checked: false }, { name: 'Trung Thong', checked: false }, { name: 'Đoàn An Nhiên', checked: false }];
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
                  listOverslept.push(item.name);
                  list += "@" + item.name + "\n";
                }
              });
              if (!list) {
                alert(messageDone)
                return messageDone;
              } else {
                alert(message + "\n" + timeAgo);
                return message;
              }
            });
          }
        }, (result) => {
          console.log('🚀 ~ result', result);
        });
      } catch (e) {
      }
    }
  }
}
</script>
