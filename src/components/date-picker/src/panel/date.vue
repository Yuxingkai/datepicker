<template>
  <transition name="el-zoom-in-top" @after-enter="handleEnter" @after-leave="handleLeave">
    <div
      v-show="visible"
      class="el-picker-panel el-date-picker el-popper"
      :class="[{
        'has-sidebar': $slots.sidebar || shortcuts,
        'has-time': showTime
      }, popperClass]">
      <div class="el-picker-panel__body-wrapper">
        <slot name="sidebar" class="el-picker-panel__sidebar"></slot>
        <div class="el-picker-panel__sidebar" v-if="shortcuts">
          <button
            type="button"
            class="el-picker-panel__shortcut"
            v-for="(shortcut, key) in shortcuts"
            :key="key"
            @click="handleShortcutClick(shortcut)">{{ shortcut.text }}</button>
        </div>
        <div class="el-picker-panel__body">
          <div class="el-date-picker__time-header" v-if="showTime">
            <span class="el-date-picker__editor-wrap" v-clickoutside="handleTimePickClose">
              <span @click="select('year')" class="classWrapFirst">
                <input @blur="blurChange" class="inputClass" v-model="ymd.y" type="text">
                <div v-if="yearShow" class="classList">
                  <div class="classScroll">
                    <ul class="classContent">
                      <li
                        @mouseenter="mouseEn(index)"
                        @mouseleave="mouseLe()"
                        :class="nowIndex === index ? 'activeClass' : ''"
                        @click.stop="choose('year', item)"
                        v-for="(item, index) in selfyear"
                      >{{ item }}</li>
                    </ul>
                  </div>
                </div>
              </span>
              <span class="paddingLr">-</span>
              <span @click="select('month')" class="classWrap">
                <input @keyup="loadNumberM($event)" @blur="blurChange" class="inputClass" v-model="ymd.m" type="text">
                <div v-if="monthShow" class="classList">
                  <div class="classScroll">
                    <ul class="classContent">
                      <li
                        @mouseenter="mouseEn(index)"
                        @mouseleave="mouseLe()"
                        :class="nowIndex === index ? 'activeClass' : ''"
                        @click.stop="choose('month', item)"
                        v-for="(item, index) in selfmonth"
                      >{{ item }}</li>
                    </ul>
                  </div>
                </div>
              </span>
              <span class="paddingLr">-</span>
              <span @click="select('day')" class="classWrap">
                <input @blur="blurChange" class="inputClass" v-model="ymd.d" type="text">
                <div v-if="dayShow" class="classList">
                  <div class="classScroll">
                    <ul class="classContent">
                      <li
                        @mouseenter="mouseEn(index)"
                        @mouseleave="mouseLe()"
                        :class="nowIndex === index ? 'activeClass' : ''"
                        @click.stop="choose('day', item)"
                        v-for="(item, index) in selfday"
                      >{{ item }}</li>
                    </ul>
                  </div>
                </div>
              </span>
            </span>
            <span class="el-date-picker__editor-wrap" v-clickoutside="handleTimePickCloseC">
              <div ref="input" style="display: flex">
                <span @click="select('hour')" class="classWrapFirst">
                <input @blur="blurChange" class="inputClass" v-model="hms.h" type="text">
                <div v-if="hourShow" class="classList">
                  <div class="classScroll">
                    <ul class="classContent">
                      <li
                        @mouseenter="mouseEn(index)"
                        @mouseleave="mouseLe()"
                        :class="nowIndex === index ? 'activeClass' : ''"
                        @click.stop="choose('hour', item)"
                        v-for="(item, index) in selfhour"
                      >{{ item }}</li>
                    </ul>
                  </div>
                </div>
              </span>
              <span class="paddingLr">:</span>
              <span @click="select('min')" class="classWrap">
                <input @blur="blurChange" class="inputClass" v-model="hms.m" type="text">
                <div v-if="minShow" class="classList">
                  <div class="classScroll">
                    <ul class="classContent">
                      <li
                        @mouseenter="mouseEn(index)"
                        @mouseleave="mouseLe()"
                        :class="nowIndex === index ? 'activeClass' : ''"
                        @click.stop="choose('min', item)"
                        v-for="(item, index) in selfmin"
                      >{{ item }}</li>
                    </ul>
                  </div>
                </div>
              </span>
              <span class="paddingLr">:</span>
              <span @click="select('sec')" class="classWrap">
                <input @blur="blurChange" class="inputClass" v-model="hms.s" type="text">
                <div v-if="secShow" class="classList">
                  <div class="classScroll">
                    <ul class="classContent">
                      <li
                        @mouseenter="mouseEn(index)"
                        @mouseleave="mouseLe()"
                        :class="nowIndex === index ? 'activeClass' : ''"
                        @click.stop="choose('sec', item)"
                        v-for="(item, index) in selfsec"
                      >{{ item }}</li>
                    </ul>
                  </div>
                </div>
              </span>
              </div>
            </span>
          </div>

          <div class="el-picker-panel__content">
            <date-table
              v-show="currentView === 'date'"
              @pick="handleDatePick"
              :selection-mode="selectionMode"
              :first-day-of-week="firstDayOfWeek"
              :value="value"
              :default-value="defaultValue ? new Date(defaultValue) : null"
              :date="date"
              :cell-class-name="cellClassName"
              :disabled-date="disabledDate">
            </date-table>
            <year-table
              v-show="currentView === 'year'"
              @pick="handleYearPick"
              :value="value"
              :default-value="defaultValue ? new Date(defaultValue) : null"
              :date="date"
              :disabled-date="disabledDate">
            </year-table>
            <month-table
              v-show="currentView === 'month'"
              @pick="handleMonthPick"
              :value="value"
              :default-value="defaultValue ? new Date(defaultValue) : null"
              :date="date"
              :disabled-date="disabledDate">
            </month-table>
          </div>
        </div>
      </div>

      <div
        class="el-picker-panel__footer"
        v-show="footerVisible && currentView === 'date'">
        <div
          @click="confirm"
          class="el-picker-panel__link-btn confirm"
        >
          确定
        </div>
        <div
          @click="changeToNow"
          class="el-picker-panel__link-btn nowBtn"
        >
          此刻
        </div>
      </div>
    </div>
  </transition>
</template>

<script type="text/babel">
import {
  formatDate,
  parseDate,
  getWeekNumber,
  isDate,
  modifyDate,
  modifyTime,
  modifyWithTimeString,
  clearMilliseconds,
  clearTime,
  prevYear,
  nextYear,
  prevMonth,
  nextMonth,
  changeYearMonthAndClampDate,
  extractDateFormat,
  extractTimeFormat,
  timeWithinRange
} from '../../../elesrc/utils/date-util'
import Clickoutside from '../../../elesrc/utils/clickoutside'
import Locale from '../../../elesrc/mixins/locale'
import ElInput from '../../../input'
// import ElButton from 'element-ui/packages/button'
import TimePicker from './time'
import YearTable from '../basic/year-table'
import MonthTable from '../basic/month-table'
import DateTable from '../basic/date-table'

export default {
  mixins: [Locale],

  directives: { Clickoutside },

  watch: {
    showTime (val) {
      /* istanbul ignore if */
      if (!val) return
      this.$nextTick(_ => {
        const inputElm = this.$refs.input.$el
        if (inputElm) {
          this.pickerWidth = inputElm.getBoundingClientRect().width + 10
        }
      })
    },

    value (val) {
      if (this.selectionMode === 'dates' && this.value) return
      if (isDate(val)) {
        this.date = new Date(val)
      } else {
        this.date = this.getDefaultValue()
      }
    },

    defaultValue (val) {
      if (!isDate(this.value)) {
        this.date = val ? new Date(val) : new Date()
      }
    },

    timePickerVisible (val) {
      if (val) this.$nextTick(() => this.$refs.timepicker.adjustSpinners())
    },

    selectionMode (newVal) {
      if (newVal === 'month') {
        /* istanbul ignore next */
        if (this.currentView !== 'year' || this.currentView !== 'month') {
          this.currentView = 'month'
        }
      } else if (newVal === 'dates') {
        this.currentView = 'date'
      }
    }
  },

  methods: {
    loadNumberM () {
      let el = event.currentTarget;
      let elValue = el.value;
      let reg = /^((?!1)\d{1,2}|12)$/;
      if (!elValue.match(reg)) {
        elValue = "";
        this.ymd.m = 1
        return false;
      } else {
        return true;
      }
    },
    blurChange () {
      this.emit(new Date(this.ymd.y, this.ymd.m - 1, this.ymd.d, this.hms.h, this.hms.m, this.hms.s), true)
    },
    mouseEn (index) {
      this.nowIndex = index
    },
    mouseLe () {
      this.nowIndex = -1
    },
    choose (param, item) {
      switch (param) {
        case 'year':
          this.ymd.y = item
          this.yearShow = false
          this.handleDatePick(new Date(this.ymd.y, this.ymd.m - 1, this.ymd.d))
          break
        case 'month':
          this.ymd.m = item
          this.monthShow = false
          this.handleDatePick(new Date(this.ymd.y, this.ymd.m - 1, this.ymd.d))
          this.selfday = []
          let date = new Date()
          let year = date.getFullYear()
          let month = this.ymd.m
          let d = new Date(year, month, 0)
          let days = d.getDate()
          for (let i = 0; i < days; i++) {
            this.selfday.push(i + 1)
          }
          break
        case 'day':
          this.ymd.d = item
          this.dayShow = false
          this.handleDatePick(new Date(this.ymd.y, this.ymd.m - 1, this.ymd.d))
          break
        case 'hour':
          this.hms.h = item
          this.hourShow = false
          this.emit(new Date(this.ymd.y, this.ymd.m - 1, this.ymd.d, this.hms.h, this.hms.m, this.hms.s), true)
          break
        case 'min':
          this.hms.m = item
          this.minShow = false
          this.emit(new Date(this.ymd.y, this.ymd.m - 1, this.ymd.d, this.hms.h, this.hms.m, this.hms.s), true)
          break
        case 'sec':
          this.hms.s = item
          this.secShow = false
          this.emit(new Date(this.ymd.y, this.ymd.m - 1, this.ymd.d, this.hms.h, this.hms.m, this.hms.s), true)
          break
        default:
          this.ymd = {
            y: '年',
            m: '月',
            d: '日'
          }
          break
      }
    },
    select (param) {
      switch (param) {
        case 'year':
          this.yearShow = true
          this.monthShow = false
          this.dayShow = false
          this.hourShow = false
          this.minShow = false
          this.secShow = false
          break
        case 'month':
          this.yearShow = false
          this.monthShow = true
          this.dayShow = false
          this.hourShow = false
          this.minShow = false
          this.secShow = false
          break
        case 'day':
          this.selfday = []
          let date = new Date()
          let year = this.ymd.y
          let month = this.ymd.m
          let d = new Date(year, month, 0)
          let days = d.getDate()
          for (let i = 0; i < days; i++) {
            this.selfday.push(i + 1)
          }
          this.yearShow = false
          this.monthShow = false
          this.dayShow = true
          this.hourShow = false
          this.minShow = false
          this.secShow = false
          break
        case 'hour':
          this.yearShow = false
          this.monthShow = false
          this.dayShow = false
          this.hourShow = true
          this.minShow = false
          this.secShow = false
          break
        case 'min':
          this.yearShow = false
          this.monthShow = false
          this.dayShow = false
          this.hourShow = false
          this.minShow = true
          this.secShow = false
          break
        case 'sec':
          this.yearShow = false
          this.monthShow = false
          this.dayShow = false
          this.hourShow = false
          this.minShow = false
          this.secShow = true
          break
        default:
          this.yearShow = false
          this.monthShow = false
          this.dayShow = false
          this.hourShow = false
          this.minShow = false
          this.secShow = false
          break
      }
    },
    proxyTimePickerDataProperties () {
      const format = timeFormat => { this.$refs.timepicker.format = timeFormat }
      const value = value => { this.$refs.timepicker.value = value }
      const date = date => { this.$refs.timepicker.date = date }
      const selectableRange = selectableRange => { this.$refs.timepicker.selectableRange = selectableRange }

      this.$watch('value', value)
      this.$watch('date', date)
      this.$watch('selectableRange', selectableRange)

      format(this.timeFormat)
      value(this.value)
      date(this.date)
      selectableRange(this.selectableRange)
    },

    handleClear () {
      this.date = this.getDefaultValue()
      this.$emit('pick', null)
    },

    emit (value, ...args) {
      if (!value) {
        this.$emit('pick', value, ...args)
      } else if (Array.isArray(value)) {
        const dates = value.map(date => this.showTime ? clearMilliseconds(date) : clearTime(date))
        this.$emit('pick', dates, ...args)
      } else {
        this.$emit('pick', this.showTime ? clearMilliseconds(value) : clearTime(value), ...args)
      }
      this.userInputDate = null
      this.userInputTime = null
    },

    // resetDate() {
    //   this.date = new Date(this.date);
    // },

    showMonthPicker () {
      this.currentView = 'month'
    },

    showYearPicker () {
      this.currentView = 'year'
    },

    // XXX: 没用到
    // handleLabelClick() {
    //   if (this.currentView === 'date') {
    //     this.showMonthPicker();
    //   } else if (this.currentView === 'month') {
    //     this.showYearPicker();
    //   }
    // },

    prevMonth () {
      this.date = prevMonth(this.date)
    },

    nextMonth () {
      this.date = nextMonth(this.date)
    },

    prevYear () {
      if (this.currentView === 'year') {
        this.date = prevYear(this.date, 10)
      } else {
        this.date = prevYear(this.date)
      }
    },

    nextYear () {
      if (this.currentView === 'year') {
        this.date = nextYear(this.date, 10)
      } else {
        this.date = nextYear(this.date)
      }
    },

    handleShortcutClick (shortcut) {
      if (shortcut.onClick) {
        shortcut.onClick(this)
      }
    },

    handleTimePick (value, visible, first) {
      if (isDate(value)) {
        const newDate = this.value
          ? modifyTime(this.value, value.getHours(), value.getMinutes(), value.getSeconds())
          : modifyWithTimeString(this.getDefaultValue(), this.defaultTime)
        this.date = newDate
        this.emit(this.date, true)
      } else {
        this.emit(value, true)
      }
      if (!first) {
        this.timePickerVisible = visible
      }
    },

    handleTimePickClose () {
      this.timePickerVisible = false
      this.yearShow = false
      this.monthShow = false
      this.dayShow = false
    },
    handleTimePickCloseC () {
      this.timePickerVisible = false
      this.hourShow = false
      this.minShow = false
      this.secShow = false
    },

    handleMonthPick (month) {
      if (this.selectionMode === 'month') {
        this.date = modifyDate(this.date, this.year, month, 1)
        this.emit(this.date)
      } else {
        this.date = changeYearMonthAndClampDate(this.date, this.year, month)
        // TODO: should emit intermediate value ??
        // this.emit(this.date);
        this.currentView = 'date'
      }
    },

    handleDatePick (value) {
      this.ymd = {
        y: value.getFullYear(),
        m: value.getMonth() + 1,
        d: value.getDate()
      }
      this.hms = {
        h: '00',
        m: '00',
        s: '00'
      }
      this.emit(new Date(this.ymd.y, this.ymd.m - 1, this.ymd.d, this.hms.h, this.hms.m, this.hms.s), true)
      if (this.selectionMode === 'day') {
        let newDate = this.value
          ? modifyDate(this.value, value.getFullYear(), value.getMonth(), value.getDate())
          : modifyWithTimeString(value, this.defaultTime)
          // change default time while out of selectableRange
        if (!this.checkDateWithinRange(newDate)) {
          newDate = modifyDate(this.selectableRange[0][0], value.getFullYear(), value.getMonth(), value.getDate())
        }
        this.date = newDate
        this.emit(this.date, this.showTime)
      } else if (this.selectionMode === 'week') {
        this.emit(value.date)
      } else if (this.selectionMode === 'dates') {
        this.emit(value, true) // set false to keep panel open
      }
    },

    handleYearPick (year) {
      if (this.selectionMode === 'year') {
        this.date = modifyDate(this.date, year, 0, 1)
        this.emit(this.date)
      } else {
        this.date = changeYearMonthAndClampDate(this.date, year, this.month)
        // TODO: should emit intermediate value ??
        // this.emit(this.date, true);
        this.currentView = 'month'
      }
    },

    changeToNow () {
      // 获取当前时间
      let nowDate = new Date()
      this.ymd = {
        y: nowDate.getFullYear(),
        m: nowDate.getMonth() + 1,
        d: nowDate.getDate()
      }
      this.hms = {
        h: nowDate.getHours() < 10 ? '0' + nowDate.getHours() : nowDate.getHours(),
        m: nowDate.getMinutes() < 10 ? '0' + nowDate.getMinutes() : nowDate.getMinutes(),
        s: nowDate.getSeconds() < 10 ? '0' + nowDate.getSeconds() : nowDate.getSeconds()
      }
      // NOTE: not a permanent solution
      //       consider disable "now" button in the future
      if ((!this.disabledDate || !this.disabledDate(new Date())) && this.checkDateWithinRange(new Date())) {
        this.date = new Date()
        this.emit(this.date)
      }
    },

    confirm () {
      if (this.selectionMode === 'dates') {
        this.emit(this.value)
      } else {
        // value were emitted in handle{Date,Time}Pick, nothing to update here
        // deal with the scenario where: user opens the picker, then confirm without doing anything
        const value = this.value
          ? this.value
          : modifyWithTimeString(this.getDefaultValue(), this.defaultTime)
        this.date = new Date(value) // refresh date
        this.emit(value)
      }
    },

    resetView () {
      if (this.selectionMode === 'month') {
        this.currentView = 'month'
      } else if (this.selectionMode === 'year') {
        this.currentView = 'year'
      } else {
        this.currentView = 'date'
      }
    },

    handleEnter () {
      document.body.addEventListener('keydown', this.handleKeydown)
    },

    handleLeave () {
      this.$emit('dodestroy')
      document.body.removeEventListener('keydown', this.handleKeydown)
    },

    handleKeydown (event) {
      const keyCode = event.keyCode
      const list = [38, 40, 37, 39]
      if (this.visible && !this.timePickerVisible) {
        if (list.indexOf(keyCode) !== -1) {
          this.handleKeyControl(keyCode)
          event.stopPropagation()
          event.preventDefault()
        }
        if (keyCode === 13 && this.userInputDate === null && this.userInputTime === null) { // Enter
          this.emit(this.date, false)
        }
      }
    },

    handleKeyControl (keyCode) {
      const mapping = {
        'year': {
          38: -4, 40: 4, 37: -1, 39: 1, offset: (date, step) => date.setFullYear(date.getFullYear() + step)
        },
        'month': {
          38: -4, 40: 4, 37: -1, 39: 1, offset: (date, step) => date.setMonth(date.getMonth() + step)
        },
        'week': {
          38: -1, 40: 1, 37: -1, 39: 1, offset: (date, step) => date.setDate(date.getDate() + step * 7)
        },
        'day': {
          38: -7, 40: 7, 37: -1, 39: 1, offset: (date, step) => date.setDate(date.getDate() + step)
        }
      }
      const mode = this.selectionMode
      const year = 3.1536e10
      const now = this.date.getTime()
      const newDate = new Date(this.date.getTime())
      while (Math.abs(now - newDate.getTime()) <= year) {
        const map = mapping[mode]
        map.offset(newDate, map[keyCode])
        if (typeof this.disabledDate === 'function' && this.disabledDate(newDate)) {
          continue
        }
        this.date = newDate
        this.$emit('pick', newDate, true)
        break
      }
    },

    handleVisibleTimeChange (value) {
      const time = parseDate(value, this.timeFormat)
      if (time && this.checkDateWithinRange(time)) {
        this.date = modifyDate(time, this.year, this.month, this.monthDate)
        this.userInputTime = null
        this.$refs.timepicker.value = this.date
        this.timePickerVisible = false
        this.emit(this.date, true)
      }
    },

    handleVisibleDateChange (value) {
      const date = parseDate(value, this.dateFormat)
      if (date) {
        if (typeof this.disabledDate === 'function' && this.disabledDate(date)) {
          return
        }
        this.date = modifyTime(date, this.date.getHours(), this.date.getMinutes(), this.date.getSeconds())
        this.userInputDate = null
        this.resetView()
        this.emit(this.date, true)
      }
    },

    isValidValue (value) {
      return value && !isNaN(value) && (
        typeof this.disabledDate === 'function'
          ? !this.disabledDate(value)
          : true
      ) && this.checkDateWithinRange(value)
    },

    getDefaultValue () {
      // if default-value is set, return it
      // otherwise, return now (the moment this method gets called)
      return this.defaultValue ? new Date(this.defaultValue) : new Date()
    },

    checkDateWithinRange (date) {
      return this.selectableRange.length > 0
        ? timeWithinRange(date, this.selectableRange, this.format || 'HH:mm:ss')
        : true
    }
  },

  components: {
    TimePicker, YearTable, MonthTable, DateTable, ElInput
  },
  data () {
    return {
      nowIndex: -1,
      ymd: {
        y: '年',
        m: '月',
        d: '日'
      },
      hms: {
        h: '时',
        m: '分',
        s: '秒'
      },
      yearShow: false,
      monthShow: false,
      dayShow: false,
      hourShow: false,
      minShow: false,
      secShow: false,
      selfhour: [
      ],
      selfmin: [],
      selfsec: [],
      selfyear: [
      ],
      selfmonth: [
        '1',
        '2',
        '3',
        '4',
        '5',
        '6',
        '7',
        '8',
        '9',
        '10',
        '11',
        '12'
      ],
      selfday: [],
      popperClass: '',
      date: new Date(),
      value: '',
      defaultValue: null, // use getDefaultValue() for time computation
      defaultTime: null,
      showTime: false,
      selectionMode: 'day',
      shortcuts: '',
      visible: false,
      currentView: 'date',
      disabledDate: '',
      cellClassName: '',
      selectableRange: [],
      firstDayOfWeek: 7,
      showWeekNumber: false,
      timePickerVisible: false,
      format: '',
      arrowControl: false,
      userInputDate: null,
      userInputTime: null
    }
  },
  created () {
    for (let i = 0; i < 24; i++) {
      let num = 0
      num = i < 10 ? '0' + i : i
      this.selfhour.push(num)
    }
    for (let i = 0; i < 60; i++) {
      let num = 0
      num = i < 10 ? '0' + i : i
      this.selfmin.push(num)
      this.selfsec.push(num)
    }
    // 获取当前时间
    let nowDate = new Date()
    for (let i = nowDate.getFullYear() - 10; i < nowDate.getFullYear() + 1; i++) {
      this.selfyear.push(i)
    }
    this.ymd = {
      y: nowDate.getFullYear(),
      m: nowDate.getMonth() + 1,
      d: nowDate.getDate()
    }
    this.hms = {
      h: nowDate.getHours() < 10 ? '0' + nowDate.getHours() : nowDate.getHours(),
      m: nowDate.getMinutes() < 10 ? '0' + nowDate.getMinutes() : nowDate.getMinutes(),
      s: nowDate.getSeconds() < 10 ? '0' + nowDate.getSeconds() : nowDate.getSeconds()
    }
  },
  computed: {
    year () {
      return this.date.getFullYear()
    },

    month () {
      return this.date.getMonth()
    },

    week () {
      return getWeekNumber(this.date)
    },

    monthDate () {
      return this.date.getDate()
    },

    footerVisible () {
      return this.showTime || this.selectionMode === 'dates'
    },

    visibleTime () {
      if (this.userInputTime !== null) {
        return this.userInputTime
      } else {
        return formatDate(this.value || this.defaultValue, this.timeFormat)
      }
    },

    visibleDate () {
      if (this.userInputDate !== null) {
        return this.userInputDate
      } else {
        return formatDate(this.value || this.defaultValue, this.dateFormat)
      }
    },

    yearLabel () {
      const yearTranslation = this.t('el.datepicker.year')
      if (this.currentView === 'year') {
        const startYear = Math.floor(this.year / 10) * 10
        if (yearTranslation) {
          return startYear + ' ' + yearTranslation + ' - ' + (startYear + 9) + ' ' + yearTranslation
        }
        return startYear + ' - ' + (startYear + 9)
      }
      return this.year + ' ' + yearTranslation
    },

    timeFormat () {
      if (this.format) {
        return extractTimeFormat(this.format)
      } else {
        return 'HH:mm:ss'
      }
    },

    dateFormat () {
      if (this.format) {
        return extractDateFormat(this.format)
      } else {
        return 'yyyy-MM-dd'
      }
    }
  }
}
</script>
<style>
  .el-date-picker__time-header {
    display: flex;
    justify-content: space-around;
    padding: 5px 0;
  }
  .el-date-picker__editor-wrap {
    padding-top: 3px;
    display: flex;
    width: 140px;
    height: 32px;
    border-radius:3px;
    border:1px solid rgba(87,149,255,1);
  }
  .el-picker-panel__link-btn {
    float: right;
  }
  .el-input {
    display: inline-block;
  }
  .confirm {
    width: 70px;
    height: 30px;
    background: #5795ff;
    text-align: center;
    line-height: 30px;
    margin-left: 10px;
    cursor: pointer;
  }
  .nowBtn {
    font-size:18px;
    font-family:PingFangSC-Medium,PingFangSC;
    font-weight:500;
    color:rgba(87,149,255,1);
    cursor: pointer;
  }
  .el-picker-panel__footer {
    height: 30px;
    padding: 20px 10px;
  }
  .el-zoom-in-top-enter-active,
  .el-zoom-in-top-leave-active {
    opacity: 1;
    transform: scaleY(1);
    transition: transform 300ms cubic-bezier(0.23, 1, 0.32, 1), opacity 300ms cubic-bezier(0.23, 1, 0.32, 1) !default;
    transform-origin: center top;
  }
  .el-zoom-in-top-enter,
  .el-zoom-in-top-leave-active {
    opacity: 0;
    transform: scaleY(0);
  }
  .classWrapFirst {
    position: relative;margin-left: 14px;cursor: pointer
  }
  .classWrap {
    position: relative;cursor: pointer
  }
  .classList {
    width: 54px;
    position: absolute;
    z-index: 1;
    top: 30px;
    left: 0;
    background: #192C57;
    box-shadow: 0 2px 14px 0 rgba(0,0,0,0.40);
    overflow: hidden;
    height: 150px;
  }
  .classScroll {
    width: 78px;height: 100%;overflow-y: scroll;
  }
  .classContent {
    margin: 0;list-style:none;padding: 0 2px;width: 54px;text-align: center
  }
  .activeClass {
    background: #233869;
  }
  .inputClass {
    text-align: center;background: none;border: none;color: white;width: 100%;height: 70%
  }
  .paddingLr {
    padding: 0 2px;
  }
</style>
