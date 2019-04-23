<template>
  <div class="component-keyboard-license-plate">
    <div class="keyboard-value" :style="{zIndex: keyboardVisible ? zIndex + 1 : 'initial'}">
      <div @click="showProvinceKeyboard" class="keyboard-value__province">
        <div class="province-val">{{provinceVal}}</div>
        <div class="arrow-down"></div>
      </div>
      <div @click="showLetterNumberKeyboard" class="keyboard-value__letter-number">
        <div class="letter-number-val">{{numberVal}}</div>
        <div v-show="keyboardVisible && type === 1" class="keyboard-value__focus-icon">0</div>
        <div class="keyboard-value__placeholder" v-if="!numberVal">{{placeholder}}</div>
      </div>
    </div>

    <div v-show="keyboardVisible" @click="closeKeyboard" class="keyboard" :style="{zIndex: zIndex}">
      <div class="keyboard__control">
        <div class="keyboard__control__handle">
          <div @click="keyboardVisible = false" class="button-sure">确定</div>
        </div>
        <!-- 省 -->
        <div v-show="type === 0" class="keyboard__control__province">
          <template v-for="(cell, index) in province">
            <div class="keyboard__control__cell" :key="index">
              <div
                v-for="(item, idx) in cell"
                @click="checkProvince(item)"
                class="item"
                :class="{empty: !item, active: provinceVal === item}"
                :key="idx">
                {{item}}
              </div>
            </div>
          </template>
        </div>
        <!-- 数字、字母 -->
        <div v-show="type === 1" class="keyboard__control__letter-number">
          <template v-for="(itemList, index) in letter">
            <div class="keyboard__control__cell" :class="{'keyboard__control__cell__style-a': index > 1 && index < 4}" :key="index">
              <template v-for="(item, idx) in itemList">
                <template v-if="disabledKeys.includes(item)">
                  <div class="item disabled" :key="idx">
                    <span>{{item}}</span>
                  </div>
                </template>
                <template v-else>
                  <template v-if="numberVal.length === 0 && (index === 0 || index === 4)">
                    <div class="item disabled" :class="{empty: !item}" :key="idx">
                      <span>{{item}}</span>
                    </div>
                  </template>
                  <template v-else>
                    <template v-if="item">
                      <div @click="checkLetterNumber(item)" class="item" :class="{empty: !item}" :key="idx">
                        <span>{{item || '0'}}</span>
                        <div v-if="item" class="tip">
                          <div class="tip__top">{{item}}</div>
                          <div class="tip__bottom"></div>
                        </div>
                      </div>
                    </template>
                    <template v-else>
                      <div v-if="index === 3 && idx === 10" @click="deleteLetterNumber" class="item empty" :key="idx">
                        <span>0</span>
                        <!-- 删除按钮样式 -->
                        <div class="delete"></div>
                      </div>
                      <div v-else class="item empty" :key="idx">
                        <span>0</span>
                      </div>
                    </template>
                  </template>
                </template>
              </template>
            </div>
          </template>
        </div>
      </div>
    </div>
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
    showProvinceKeyboard () {
      this.type = 0
      this.keyboardVisible = true
    },
    checkProvince (item) {
      this.type = 1
      this.$emit('input', item + this.numberVal)
    },
    showLetterNumberKeyboard () {
      this.type = 1
      this.keyboardVisible = true
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
    closeKeyboard (event) {
      if (event.target.className === 'keyboard') {
        this.keyboardVisible = false
      }
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
      font-weight: bold;
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
  .keyboard {
    position: fixed;
    top: 0;
    left: 0;
    width: 100%;
    height: 100%;
    font-size: inherit;
    font-weight: bold;
    user-select: none;
    overflow: hidden;
    &__control {
      position: absolute;
      bottom: 0;
      left: 0;
      width: 100%;
      padding-bottom: 20px;
      background: @keyboardBg;
      &__handle {
        height: 44px;
        line-height: 44px;
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
        display: block;
        padding-top: 10px;
      }
      &__letter-number {
        display: block;
        padding-top: 10px;
      }
      &__cell {
        position: relative;
        display: flex;
        align-items: center;
        padding: 0 4px;
        justify-content: flex-start;
        height: 44px;
        line-height: 44px;
        margin-bottom: 12px;
        .item {
          position: relative;
          flex: 1;
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
          &:active .tip {
            display: block;
          }
        }
        .tip {
          position: absolute;
          bottom: 0;
          left: 0;
          width: 100%;
          display: none;
          &__top {
            box-sizing: border-box;
            position: relative;
            top: 2px;
            margin-left: -30%;
            width: 160%;
            height: 58px;
            font-size: 180%;
            border-radius: 8px;
            border-bottom-left-radius: 20px;
            border-bottom-right-radius: 20px;
            box-shadow: 0 2px 8px @keyboardDark;
            background: #fff;
          }
          &__bottom {
            box-sizing: border-box;
            position: relative;
            width: 100%;
            height: 50px;
            box-shadow: 0 6px 6px @keyboardDark;
            border-bottom-left-radius: 3px;
            border-bottom-right-radius: 3px;
            background: #fff;
          }
        }
        &__style-a .item:first-child, &__style-a .item:last-child {
          flex: 0.5;
        }
      }
    }
    .delete {
      position: absolute;
      top: 0;
      right: 0;
      width: 300%;
      height: 100%;
      display: flex;
      align-items: center;
      justify-content: center;
      border-radius: 4px;
      text-align: center;
      border-bottom: 1px solid @keyboardShadow;
      background: url(@deleteImg) center center / auto 60% no-repeat;
      background-color: @keyboardDark;
      color: #000;
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
</style>

