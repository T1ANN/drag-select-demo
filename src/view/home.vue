<template>
  <div id="container" @mousedown="onmousedown" @mouseup="onmouseup" @mousemove="onmousemove">
    <div id="selectArea"></div>
    <div class="box" v-for="item in divData">
<!--      <div>{{item.label}}</div>-->
    </div>
  </div>
</template>

<script>
  import $ from 'jquery'
  import 'jquery-ui/ui/widgets/selectable'

  export default {
    name: 'home',
    data() {
      return {
        container: document.getElementById("container"),
        selectArea: document.getElementById("selectArea"),
        start: {x: 0, y: 0},
        mouseDown: 0,
        divData: [],
      }
    },
    methods: {
      getPosition() {
        var e = window.event || arguments[0];
        return {
          x: e.pageX || (e.clientX + (document.documentElement.scrollLeft || document.body.scrollLeft)),
          y: e.pageY || (e.clientY + (document.documentElement.scrollTop || document.body.scrollTop))
        };
      },
      hasClass(elem, cls) {
        cls = cls || '';
        if (cls.replace(/\s/g, '').length === 0) return false;
        return new RegExp(' ' + cls + ' ').test(' ' + elem.className + ' ');
      },
      addClass(elem, cls) {
        if (!this.hasClass(elem, cls)) {
          elem.className = elem.className === '' ? cls : elem.className + ' ' + cls;
        }
      },
      removeClass(elem, cls) {
        if (this.hasClass(elem, cls)) {
          var newClass = ' ' + elem.className.replace(/[\t\r\n]/g, '') + ' ';
          while (newClass.indexOf(' ' + cls + ' ') >= 0) {
            newClass = newClass.replace(' ' + cls + ' ', ' ');
          }
          elem.className = newClass.replace(/^\s+|\s+$/g, '');
        }
      },
      isIn(selDiv, region) {
        var st = this.container.scrollTop;
        var s_top = parseInt(selDiv.offsetTop);
        var s_left = parseInt(selDiv.offsetLeft);
        var s_right = s_left + parseInt(selDiv.offsetWidth);
        var s_bottom = s_top + parseInt(selDiv.offsetHeight);
        var r_top = parseInt(region.offsetTop) + parseInt(this.container.scrollTop);
        var r_left = parseInt(region.offsetLeft) + parseInt(this.container.scrollLeft);
        var r_right = r_left + parseInt(region.offsetWidth);
        var r_bottom = r_top + parseInt(region.offsetHeight);
        var t = Math.max(s_top, r_top);
        var r = Math.min(s_right, r_right);
        var b = Math.min(s_bottom, r_bottom);
        var l = Math.max(s_left, r_left);
        if (b > t + 5 && r > l + 5) {
          return selDiv;
        } else {
          return null;
        }
      },
      onmousedown() {
        this.clearSelect();
        var e = window.event || arguments[0];
        this.start = this.getPosition(e);
        this.mouseDown = 1;
      },
      onmouseup() {
        this.selectArea.style.display = "none";
        this.mouseDown = 0;
      },
      onmousemove() {
        var e = window.event || arguments[0];
        var divs = document.getElementsByClassName("box");
        if (this.mouseDown) {
          this.selectArea.style.display = "block";
          this.selectArea.style.left = Math.min(this.start.x, this.getPosition(e).x) + "px";
          this.selectArea.style.top = Math.min(this.start.y, this.getPosition(e).y) + "px";
          this.selectArea.style.width = Math.abs(this.start.x - this.getPosition(e).x) + "px";
          this.selectArea.style.height = Math.abs(this.start.y - this.getPosition(e).y) + "px";
          for (var i = 0; i < divs.length; i++) {
            if (this.isIn(divs[i], this.selectArea)) {
              this.addClass(divs[i], "select");
            } else {
              this.removeClass(divs[i], "select");
            }
          }
        }
      },
      clearSelect() {
        var divs = document.getElementsByClassName("box");
        for (var i = 0; i < divs.length; i++) {
          this.removeClass(divs[i], "select");
        }
      },
    },
    mounted() {
      for (let i = 0; i < 1000; i++) {
        this.$data.divData.push({label: i});
      }
      this.$nextTick(function () {
        //方法一：jquery-ui selectable方法实现
        // $("#container").selectable();
        //方法二：js实现
        this.container = document.getElementById("container");
        this.selectArea = document.getElementById("selectArea");
      });
    },
  }
</script>
<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
  * {
    padding: 0px;
    margin: 0px;
  }

  #container {
    width: 1000px;
    height: 500px;
    margin: 50px auto;
  }

  #selectArea {
    position: absolute;
    width: 0px;
    height: 0px;
    display: none;
    font-size: 0px;
    margin: 0px;
    padding: 0px;
    opacity: 0.5;
    top: 0px;
    left: 0px;
    background: inherit;
    border: 1px dashed rgb(0, 0, 0);
    z-index: 1000;
  }

  .box {
    width: 88px;
    height: 88px;
    border: 1px solid #aaa;
    margin: 0 -1px -1px 0;
    float: left;
    transition: all .7s;
  }

  .select {
    background: #409EFF;
  }

  .ui-selecting {
    background: rgb(140, 197, 255);
  }

  .ui-selected {
    background: #409EFF;
  }
</style>
