<template>
  <div class="component-keyboard-license-plate">
    <div class="keyboard-value" :style="{zIndex: keyboardVisible ? zIndex + 1 : 'initial'}">
      <div @click="focus(0)" class="keyboard-value__province">
        <div class="province-val">{{provinceVal}}</div>
        <div class="arrow-down"></div>
      </div>
      <div @click="focus(1)" class="keyboard-value__letter-number">
        <div class="letter-number-val">{{numberVal}}</div>
        <div v-show="keyboardVisible && type === 1" class="keyboard-value__focus-icon">0</div>
        <div class="keyboard-value__placeholder" v-if="!numberVal">{{placeholder}}</div>
      </div>
    </div>

    <div v-show="keyboardVisible" @click="keyboardVisible = false" class="keyboard-mask" :style="{zIndex: zIndex}"></div>

    <transition name="keyboard-control">
      <div v-show="keyboardVisible" class="keyboard-control" :style="{zIndex: zIndex + 1}">
        <div class="keyboard-control__handle">
          <div @click="keyboardVisible = false" class="button-sure">确定</div>
        </div>
        <!-- 省 -->
        <div v-show="type === 0" class="keyboard-control__province">
          <template v-for="(cell, index) in province">
            <div class="keyboard-control__cell" :key="index">
              <div
                v-for="(item, idx) in cell"
                @click="checkProvince(item)"
                class="keyboard-control__cell__item"
                :class="{empty: !item, active: provinceVal === item}"
                :key="idx">
                {{item}}
              </div>
            </div>
          </template>
        </div>
        <!-- 数字、字母 -->
        <div v-show="type === 1" class="keyboard-control__letter-number">
          <template v-for="(itemList, index) in letter">
            <div class="keyboard-control__cell" :class="{'keyboard-control__cell__style-a': index > 1 && index < 4}" :key="index">
              <template v-for="(item, idx) in itemList">
                <template v-if="disabledKeys.includes(item)">
                  <div class="keyboard-control__cell__item disabled" :key="idx">
                    <span>{{item}}</span>
                  </div>
                </template>
                <template v-else>
                  <template v-if="numberVal.length === 0 && (index === 0 || index === 4)">
                    <div class="keyboard-control__cell__item" :class="{empty: !item, disabled: !!item}" :key="idx">
                      <span>{{item}}</span>
                    </div>
                  </template>
                  <template v-else>
                    <div @click="checkLetterNumber(item)" class="keyboard-control__cell__item" :class="{empty: !item}" :key="idx">
                      <span>{{item || '0'}}</span>
                    </div>
                    <template v-if="index === 3 && idx === 10">
                      <div @click="deleteLetterNumber" class="keyboard-control__delete" :key="idx + 1"></div>
                    </template>
                  </template>
                </template>
              </template>
            </div>
          </template>
        </div>
      </div>
    </transition>
  </div>
</template>

<script>
const province = [
  ['京', '津', '沪', '渝', '冀', '豫', '云'],
  ['辽', '黑', '湘', '皖', '鲁', '新', '苏'],
  ['浙', '赣', '鄂', '桂', '甘', '晋', '蒙'],
  ['陕', '吉', '闽', '贵', '粤', '青', '藏'],
  ['川', '宁', '琼', '', '', '', '']
]
const letter = [
  ['1', '2', '3', '4', '5', '6', '7', '8', '9', '0'],
  ['Q', 'W', 'E', 'R', 'T', 'Y', 'U', 'I', 'O', 'P'],
  ['', 'A', 'S', 'D', 'F', 'G', 'H', 'J', 'K', 'L', ''],
  ['', '', 'Z', 'X', 'C', 'V', 'B', 'N', 'M', '', ''],
  ['', '', '港', '澳', '学', '警', '领', '使', '', '']
]
const provinceFormat = province.flat()
const letterFormat = letter.flat()
const reg7 = /^[京沪浙苏粤鲁晋冀豫川渝辽吉黑皖鄂湘赣闽陕甘宁蒙津贵云桂琼青新藏]{1}[a-hj-np-zA-HJ-NP-Z]{1}[a-hj-np-zA-HJ-NP-Z0-9]{4}[a-hj-np-zA-HJ-NP-Z0-9\u4e00-\u9fa5]$/g
const reg8 = /^[京沪浙苏粤鲁晋冀豫川渝辽吉黑皖鄂湘赣闽陕甘宁蒙津贵云桂琼青新藏]{1}[a-hj-np-zA-HJ-NP-Z]{1}([0-9]{5}[d|f|D|F]|[d|f|D|F][a-hj-np-zA-HJ-NP-Z0-9][0-9]{4})$/g

const formatVal = function (value, defaultProvince = '湘') {
  let provinceVal = value.substring(0, 1)
  let letterVal = value.substring(1, 2)
  let numberVal = ''
  let currentValue = ''
  let valid = false
  let hasProvinceVal = false
  if (provinceFormat.some(v => v === provinceVal)) {
    value = value.replace(provinceVal, '')
    hasProvinceVal = true
  } else {
    provinceVal = ''
  }
  if (letterFormat.some(v => v === letterVal) && hasProvinceVal) {
    value = value.replace(letterVal, '')
  } else {
    letterVal = ''
  }
  provinceVal = provinceVal || defaultProvince
  numberVal = letterVal + value.substring(0, 6).toUpperCase()
  currentValue = provinceVal + numberVal
  if (currentValue.length === 7) {
    reg7.lastIndex = 0
    valid = reg7.test(currentValue)
  } else if (currentValue.length === 8) {
    reg8.lastIndex = 0
    valid = reg8.test(currentValue)
  } else {
    valid = false
  }
  
  return {
    provinceVal,
    numberVal,
    currentValue,
    valid
  }
}

export default {
  name: 'keyboard-license-plate',
  props: {
    placeholder: {
      type: String,
      default: '车牌号码'
    },
    defaultProvince: String,
    value: String,
    zIndex: {
      type: Number,
      default: 5000
    },
    disabledKeys: {
      type: Array,
      default () {
        return ['I', 'O']
      }
    }
  },
  data () {
    return {
      ...formatVal((this.value || '').trim(), this.defaultProvince),
      province,
      letter,
      type: 0,
      keyboardVisible: false,
      deleteTimer: null
    }
  },
  methods: {
    checkProvince (item) {
      this.type = 1
      this.$emit('input', item + this.numberVal)
    },
    checkLetterNumber (item) {
      const numberVal = this.numberVal + item
      if (numberVal.length > 7) {
        return
      }
      this.numberVal = numberVal
      this.currentValue = this.provinceVal + this.numberVal
    },
    deleteLetterNumber () {
      const len = this.numberVal.length
      if (len === 0) {
        this.type = 0
      } else {
        this.numberVal = this.numberVal.substring(0, len - 1)
        this.currentValue = this.provinceVal + this.numberVal
      }
    },
    focus (type = 0) {
      this.type = type
      this.keyboardVisible = true
    },
    blur () {
      this.keyboardVisible = false
    }
  },
  watch: {
    value (val) {
      const {
        provinceVal,
        numberVal,
        currentValue,
        valid
      } = formatVal(val)
      this.provinceVal = provinceVal
      this.numberVal = numberVal
      this.currentValue = currentValue
      this.valid = valid
    },
    currentValue (val) {
      clearInterval(this.deleteTimer)
      this.deleteTimer = setTimeout(() => {
        this.$emit('input', val)
      }, 200)
    }
  }
}
</script>

<style lang="less" scoped>
@themeColor: #009cff;
@keyboardBg: #D1D2D7;
@keyboardDark: #A9AEBB;
@keyboardShadow: #87868F;
@deleteImg: "data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAACAAAAAgCAMAAABEpIrGAAAAb1BMVEUAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAABt6r1GAAAAJHRSTlMAShcaE9n04QP43ncl/erV0VceCPHm4sa6rod8azkxDezKs0TMeCevAAAArUlEQVQ4y+WSVxaCMBBFNY0qHRuK7e1/jQISJyFkBbyflLln+m5r2h+wlCwN+z2Cq5zsfYNLaLsMr8D/kT5xDp2gBFQtTvV4ETdBBwEsRvGYbgmkGN4SiQmIfPyexANINvABM4D6hSMfTk0ohYCbObyRVZQZjwDFrSQLZOkCYBZQtU6ImFtlppk/yZlovGXO6tXcKNb9GtVpDzQqX6u1PpFnWJ51oHGTytWF2Yq+LLYYZPsT+DEAAAAASUVORK5CYII=";

.component-keyboard-license-plate {
  display: inline-block;
  width: 100%;
  font-size: inherit;
  line-height: inherit;
  color: #000;
  .keyboard-value {
    position: relative;
    display: flex;
    align-items: center;
    font-size: inherit;
    &__province {
      display: flex;
      align-items: center;
      margin-right: 5px;
    }
    &__letter-number {
      position: relative;
      display: flex;
      align-items: center;
      flex: 1;
      overflow: hidden;
    }
    &__placeholder {
      color: #969799;
      user-select: none;
    }
    &__focus-icon {
      width: 2px;
      color: transparent;
      overflow: hidden;
      background: @themeColor;
      animation: opacity-frame 1.2s infinite;
    }
  }
  .keyboard-mask {
    position: fixed;
    top: 0;
    left: 0;
    width: 100%;
    height: 100%;
    font-size: inherit;
    overflow: hidden;
  }
  .keyboard-control {
    box-sizing: content-box;
    position: fixed;
    bottom: 0;
    left: 0;
    display: flex;
    flex-direction: column;
    width: 100%;
    height: 38%;
    padding-top: 40px;
    padding-bottom: 20px;
    font-weight: bold;
    background: @keyboardBg;
    &__handle {
      position: absolute;
      top: 0;
      left: 0;
      width: 100%;
      height: 40px;
      line-height: 40px;
      text-align: right;
      border-top: 1px solid @keyboardBg;
      background: lighten(@keyboardBg, 10%);
      .button-sure {
        display: inline-block;
        padding: 0 10px;
        color: @themeColor;
      }
    }
    &__province {
      flex: 1;
      display: flex;
      padding-top: 10px;
      flex-direction: column;
    }
    &__letter-number {
      flex: 1;
      display: flex;
      padding-top: 10px;
      flex-direction: column;
    }
    &__cell {
      position: relative;
      flex: 1;
      display: flex;
      align-items: stretch;
      padding: 0 4px;
      margin-bottom: 8px;
      justify-content: flex-start;
      user-select: none;
      &:last-child {
        margin-bottom: 8px;
      }
      &__item {
        position: relative;
        flex: 1;
        display: flex;
        align-items: center;
        justify-content: center;
        margin-right: 6px;
        text-align: center;
        border-radius: 4px;
        background: #fff;
        border-bottom: 1px solid @keyboardShadow;
        &.empty {
          border: none;
          color: transparent;
          background: transparent;
        }
        &.disabled {
          color: #969799;
          background: rgba(255, 255, 255, .6);
        }
        &.active {
          color: @themeColor;
        }
        &:last-child {
          margin-right: 0;
        }
        &:not(.empty):active .tip {
          display: block;
        }
      }
      &__style-a .item:first-child, &__style-a .item:last-child {
        flex: 0.5;
      }
    }
    &__delete {
      box-sizing: border-box;
      position: absolute;
      top: 0;
      right: 4px;
      width: 15%;
      height: 100%;
      border-radius: 4px;
      border-bottom: 1px solid @keyboardShadow;
      background: url(@deleteImg) center center / auto 50% no-repeat;
      background-color: @keyboardDark;
      &:active {
        background-color: #fff;
      }
    }
  }
  .arrow-down {
    position: relative;
    width: 16px;
    height: 16px;
    margin-left: 5px;
    overflow: hidden;
    &:before {
      content:'';
      position:absolute;
      bottom: 8px;
      left: 5px;
      height: 6px;
      width: 6px;
      border: 1px solid #333;
      border-right-width: 0;
      border-top-width: 0;
      transform: rotate(-45deg);
    }
  }
}

@keyframes opacity-frame {
  0% {
    opacity: 1;
  }
  60% {
    opacity: 1;
  }
  100% {
    opacity: 0;
  }
}

.keyboard-control-enter-active, .keyboard-control-leave-active {
  transition-timing-function: linear;
  transition-duration: 200ms;
}
.keyboard-control-enter, .keyboard-control-leave-to {
  transform: translate(0, 100%);
}
</style>

