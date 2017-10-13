<template>
  <div>
    <el-select @change="recordBegin" v-model="keys.amount" :placeholder="keys.keyNotice">
      <el-option
        v-for="item in keys.options"
        :key="item.value"
        :label="item.label"
        :value="item.value">
      </el-option>
    </el-select>
    <span class="fake-btn fake-btn-default" @click="recordBegin">Layout Setting</span>
    <br>
    <span class="fake-btn fake-btn-warning" @click="resetData" id="reset">Reset</span>
    <span class="fake-btn fake-btn-danger" @click="stopRecord" id="stop">Stop</span>
    <span class="fake-btn fake-btn-success" @click="startRecord" id="start">Start</span>
    <div id="main">
      <div id="chart">
        <canvas id="kps-chart"></canvas>
      </div>

      <div id="kps">
        <p id="kps-label">KPS</p>
        <p id="kps-data">{{ currentKPS }}</p>
        <p id="kps-max">max: {{ maxKPS }}</p>
      </div>

      <div id="key-setting">
        <!-- -<span>{{ key.key }}</span>- is a hack to solve the offset of Space -->
        <ul id="key-group">
          <li class="key-item" v-bind:class="{ active: key.status }" v-for="key in keys.layout">-<span>{{ key.key }}</span>-</li>
        </ul>
      </div>
    </div>
  </div>
</template>

<script>
const keysOptions = [
  {
    value: 1,
    label: '1 Key'
  },
  {
    value: 2,
    label: '2 Keys'
  },
  {
    value: 3,
    label: '3 Keys'
  },
  {
    value: 4,
    label: '4 Keys'
  },
  {
    value: 5,
    label: '5 Keys'
  },
  {
    value: 6,
    label: '6 Keys'
  },
  {
    value: 7,
    label: '7 Keys'
  },
  {
    value: 8,
    label: '8 Keys'
  },
  {
    value: 9,
    label: '9 Keys'
  },
  {
    value: 10,
    label: '10 Keys'
  }
]

export default {
  name: 'Tester',
  data () {
    return {
      keys: {
        keyNotice: 'Select key amount',
        amount: 4,
        settingTodo: 0,
        options: keysOptions,
        layoutList: ['d', 'f', 'j', 'k'],
        layout: [
          {key: 'd', status: false},
          {key: 'f', status: false},
          {key: 'j', status: false},
          {key: 'k', status: false}
        ]
      },
      isRecording: false,
      kpsdata: [],
      timedata: [],
      pressData: [],
      startTime: 0,
      currentKPS: 0,
      maxKPS: 0
    }
  },
  methods: {
    keyDown(e) {
      if (!this.keys.settingTodo) {
        let pos = this.keys.layoutList.indexOf(e.key)
        if (pos > -1) {
          // good key
          this.keys.layout[pos].status = true
          let pressTime = new Date().getTime()
          if (!this.startTime) {
            this.startTime = Math.floor(pressTime / 100)
            this.pressData.push([])
            this.pressData[0].push(e.key)
          } else {
            let dataPos = Math.floor(pressTime / 100) - this.startTime
            while (this.pressData.length <= dataPos) {
              this.pressData.push([])
            }
            this.pressData[dataPos].push(e.key)
            console.log(dataPos, this.pressData.length)
          }
        }
      } else {
        let pos = this.keys.layoutList.indexOf(e.key)
        if (pos < 0) {
          // good key
          let key = e.key
          if (key == 'Shift') key = 'S.'
          else if (key == 'ArrowUp') key = '↑'
          else if (key == 'ArrowDown') key = '↓'
          else if (key == 'ArrowLeft') key = '←'
          else if (key == 'ArrowRight') key = '→'
          else if (key.length > 1) key = key[0]
          this.keys.layoutList[this.keys.amount - this.keys.settingTodo] = e.key
          this.keys.layout[this.keys.amount - this.keys.settingTodo].key = key
          this.keys.settingTodo--
        }
      }
    },
    keyUp(e) {
      if (!this.keys.settingTodo) {
        let pos = this.keys.layoutList.indexOf(e.key)
        if (pos > -1) {
          // good key
          this.keys.layout[pos].status = false
        } 
      } else {
        
      }
    },
    recordBegin() {
      this.keys.settingTodo = this.keys.amount
      this.keys.layoutList = []
      this.keys.layout = []
      this.pressData = []
      this.startTime = 0
      for (let i = 0; i < this.keys.amount; i++) {
        this.keys.layoutList.push('')
        this.keys.layout.push({
          key: '-',
          status: false
        })
      }
    },
    stopRecord() {
      this.isRecording = false
    },
    startRecord() {
      this.isRecording = true
    },
    resetData() {
      this.isRecording = false
      while (this.chart.data.labels.length > 0) {
        this.chart.data.labels.pop()
      }
      while (this.chart.data.datasets[0].data.length > 0) {
        this.chart.data.datasets[0].data.pop()
      }
      this.kpsdata = []
      this.timedata = []
      this.pressData = []
      this.startTime = 0
      this.maxKPS = 0
      this.chart.update()
    },
    refresh() {
      if (this.startTime && this.isRecording) {
        if (this.pressData.length >= 10) {
          let total = 0, index = this.pressData.length - 1
          for (let i = 0; i < 10; i++) {
            total += this.pressData[index - i].length
          }
          this.currentKPS = total
        } else {
          let total = 0, length = this.pressData.length
          for (let i = 0; i < length; i++) {
            total += this.pressData[i].length
          }
          this.currentKPS = Math.floor(total / length)
        }
        if (this.currentKPS > this.maxKPS) this.maxKPS = this.currentKPS
        let pressTime = new Date().getTime()
        let dataPos = Math.floor(pressTime / 100) - this.startTime
        while (this.pressData.length <= dataPos) {
          this.pressData.push([])
        }
        this.kpsdata.push(this.currentKPS)
        // * 10 / 10 to fix float bug
        this.timedata.push(Math.floor(0.2 * this.timedata.length * 10) / 10)
        this.chart.data.labels.push(this.timedata[this.timedata.length - 1])
        this.chart.data.datasets[0].data.push(this.kpsdata[this.kpsdata.length - 1])
        this.chart.update()
      } else {
        this.currentKPS = 0
      }
    }
  },
  beforeRouteEnter (to, from, next) {
    next(vm => {
      window.addEventListener('keydown', vm.keyDown)
      window.addEventListener('keyup', vm.keyUp)
      setInterval(vm.refresh, 200)
      let ctx = document.getElementById("kps-chart")
      vm.chart = new Chart(ctx, {
        type: 'line',
        data: {
          labels: vm.timedata,
          datasets: [
            {
              fill: false,
              backgroundColor: '#4486ea',
              borderColor: '#4486ea',
              data: vm.kpsdata,
            }
          ]
        },
        options: {
          animation: {
            duration: 0,
          },
          responsiveAnimationDuration: 0,
          responsive: true,
          title:{
            display:true,
            text:'KPS Chart'
          },
          legend: {
            display: false
          },
          tooltips: {
            mode: 'index',
            intersect: false,
          },
          hover: {
            animationDuration: 0,
            mode: 'nearest',
            intersect: true
          },
          layout: {
            padding: {
              top: 0
            }
          },
          elements: { point: { radius: 0 } },
          scales: {
            xAxes: [{
              display: true,
              scaleLabel: {
                display: true,
                labelString: 'Time'
              },
              gridLines: {
                display:false
              }
            }],
            yAxes: [{
              display: true,
              scaleLabel: {
                display: true,
                labelString: 'KPS'
              },
              ticks: {
                suggestedMin: 0,
                suggestedMax: 150
              },
              gridLines: {
                display:false
              }
            }]
          }
        }
      })
    })
  }
}
</script>

<style scoped>
#reset {
  margin: 50px 0;
}

#main {
  width: 100%;
  width: 100vw;
}

#main > div {
  width: 33%;
  float: left;
}

#chart {
  height: 300px;
}

#kps-label {
  margin: 0;
  color: #333333;
  font-size: 50px;
  font-weight: bold;
}
#kps-data {
  margin: 0;
  color: #333333;
  font-size: 50px;
  font-weight: bold;
}

#key-group {

}
.key-item {
  width: 40px;
  max-width: 9%;
  height: 80px;
  line-height: 80px;
  font-size: 20px;
  color: transparent;
  border: 1px solid #999;
  display: inline-block;
}
.key-item > span {
  color: #666;
}
.key-item.active {
  background-color: #ff5f5f;
}

.fake-btn {
  display: inline-block;
  line-height: 1;
  white-space: nowrap;
  cursor: pointer;
  background: #fff;
  border: 1px solid #c4c4c4;
  color: #1f2d3d;
  margin: 0;
  padding: 10px 15px;
  border-radius: 4px;
}
.fake-btn-default:hover {
  color: #fff;
  background-color: #20a0ff;
  border-color: #20a0ff;
}
.fake-btn-warning:hover {
  color: #fff;
  background-color: #f7ba2a;
  border-color: #f7ba2a;
}
.fake-btn-danger:hover {
  color: #fff;
  background-color: #ff4949;
  border-color: #ff4949;
}
.fake-btn-success:hover {
  color: #fff;
  background-color: #13ce66;
  border-color: #13ce66;
}
</style>