<template>
  <div class="container">
    <!--    <header class="header">
          <h1>2048</h1>
          <button class="new-btn">New Game</button>
          <p class="score">
            <span>0</span>
          </p>
        </header>-->
    <div id="grid-container" @touchmove="handletouchmove" @touchstart="handletouchtart" @touchend="handletouchend">
      <!--背景-->
      <block v-for="(r,rIndex) in data" v-bind:key="r">
        <block v-for="(c,cIndex) in r" v-bind:key="c">
          <div  class="grid-cell"
               :id="'grid-cell-'+rIndex+'-'+cIndex">
          </div>
        </block>
      </block>
      <!--前景-->
        <block v-for="(r,rIndex) in data">
          <block v-for="(c,cIndex) in r">
            <div  :class="['grid-cell','bg'+c]"
                  :style="{color:c <= 4 ? '#776e65' : 'white'}"
                  :id="'num-grid-cell-'+rIndex+'-'+cIndex" v-text=" c >0 ? c :''">
            </div>
          </block>
        </block>
    </div>
  </div>
</template>
<script>
  export default {
    data () {
      return {
        RN: 4,
        CN: 4,
        data: null,
        score: 0,
        state: 1,
        GAMEOVER: 0,
        RUNNING: 1,
        lastX: 0,
        lastY: 0,
        flag: 0
      }
    },
    methods: {
      start () {
        let data = []
        for (let r = 0; r < this.RN; r++) {
          data[r] = []
          for (let c = 0; c < this.CN; c++) {
            data[r][c] = 0
          }
        }
        this.data = data
        this.randomNum()
        this.randomNum()
      },
      move (callback) {
        let before = String(this.data)
        callback()
        let after = String(this.data)
        if (before !== after) {
          this.randomNum()
          if (this.isGameOver()) {
            this.state = this.GAMEOVER
          }
        }
      },
      isGameOver () {
        for (let r = 0; r < this.RN; r++) {
          for (let c = 0; c < this.CN; c++) {
            if (this.data[r][c] === 0) {
              return false
            } else if (c < this.CN - 1 && this.data[r][c] === this.data[r][c + 1]) {
              return false
            } else if (r < this.RN - 1 && this.data[r][c] === this.data[r + 1][c]) {
              return false
            }
          }
        }
        return true
      },
      moveLeft () {
        let that = this
        this.move(function () {
          for (let r = 0; r < that.RN; r++) {
            that.moveLeftInRow(r)
          }
        })
      },
      moveLeftInRow (r) {
        for (let c = 0; c < this.CN - 1; c++) {
          /* eslint-disable */
          let nextc = this.getNextInRow(r,c)
          if (nextc === -1) {
            break
          } else {
            if (this.data[r][c] === 0) {
              this.data[r][c] = this.data[r][nextc]
              this.data[r][nextc] = 0
              c--
            } else if (this.data[r][c] === this.data[r][nextc]) {
              this.data[r][c] *= 2
              this.score += this.data[r][c]
              this.data[r][nextc] = 0
            }
          }
        }
      },
      getNextInRow (r,c) {
        c++;
        for(;c < this.CN;c++){
          if(this.data[r][c] !==0){
            return c
          }
        }
        return -1;
      },
      moveRight () {
        let that=this;
        this.move(function(){
          for(let r=0;r<that.RN;r++){
            that.moveRightInRow(r)
          }
        })
      },
      moveRightInRow (r) {
        for(let c=this.CN-1;c>0;c--){
          let prevc=this.getPrevInRow(r,c);
          if(prevc === -1){break;}
          else{
            if(this.data[r][c] === 0){
              this.data[r][c]=this.data[r][prevc];
              this.data[r][prevc]=0;
              c++;
            }else if(this.data[r][c] === this.data[r][prevc]){
              this.data[r][c]*=2;
              this.score+=this.data[r][c];
              this.data[r][prevc]=0;
            }
          }
        }
      },
      getPrevInRow (r,c) {
        c--;
        for(;c>=0;c--){
          if(this.data[r][c]!==0){
            return c;
          }
        }
        return -1;
      },
      moveUp () {
        let that=this;
        this.move(function(){
          for(let c=0;c<that.CN;c++){
            that.moveUpInCol(c);
          }
        });
      },
      moveUpInCol(c){
        for(let r=0;r<this.RN-1;r++){
          let nextr=this.getNextInCol(r,c);
          if(nextr===-1){break;}
          else{
            if(this.data[r][c]===0){
              this.data[r][c]=this.data[nextr][c];
              this.data[nextr][c]=0;
              r--;
            }else if(this.data[r][c]
              ===this.data[nextr][c]){
              this.data[r][c]*=2;
              this.score+=this.data[r][c];
              this.data[nextr][c]=0;
            }
          }
        }
      },
      getNextInCol (r,c){
        r++;
        for(;r<this.RN;r++){
          if(this.data[r][c]!==0){
            return r;
          }
        }
        return -1;
      },
      moveDown(){
        let that=this
        this.move(function(){
          for(let c=0;c<that.CN;c++){
            that.moveDownInCol(c);
          }
        });
      },
      moveDownInCol(c){
        for(let r=this.RN-1;r>0;r--){
          let prevr=this.getPrevInCol(r,c);
          if(prevr===-1){break;}
          else{
            if(this.data[r][c]===0){
              this.data[r][c]=this.data[prevr][c];
              this.data[prevr][c]=0;
              r++;
            }else if(this.data[r][c]
              ===this.data[prevr][c]){
              this.data[r][c]*=2;
              this.score+=this.data[r][c];
              this.data[prevr][c]=0;
            }
          }
        }
      },
      getPrevInCol:function(r,c){
        r--;
        for(;r>=0;r--){
          if(this.data[r][c]!==0)
            return r;
        }
        return -1;
      },
      randomNum(){
        while(true){
          let r=Math.floor(Math.random()*(this.RN));
          let c=Math.floor(Math.random()*(this.CN));
          if(this.data[r][c]===0){
            this.data[r][c]=Math.random()<0.5?2:4;
            break;
          }
        }
      },
      getNumberColor (number) {
        if (number <= 4) {
          return '#776e65'
        }
        return 'white'
      },
      handletouchmove: function(event) {
        if (this.flag!==0){
          return
        }
        let currentX = event.touches[0].pageX;
        let currentY = event.touches[0].pageY;
        let tx = currentX - this.lastX;
        let ty = currentY - this.lastY;
        //左右方向滑动
        if (Math.abs(tx) > Math.abs(ty)) {
          if (tx < 0) {
            this.flag= 1
          }
          else if (tx > 0) {
            this.flag= 2
          }

        }
        //上下方向滑动
        else {
          if (ty < 0){
            this.flag= 3
          }
          else if (ty > 0) {
            this.flag= 4
          }
        }
        //将当前坐标进行保存以进行下一次计算
        this.lastX = currentX;
        this.lastY = currentY;
      },
      handletouchtart:function(event) {
        this.lastX = event.touches[0].pageX;
        this.lastY = event.touches[0].pageY;
      },
      handletouchend:function(event) {
        this.flag= 0;
      }
    },
    mounted (){
      this.start()
    },
    watch:{
      flag(val){
        switch (val){
          case 1 : this.moveLeft();
            break;
          case 2: this.moveRight();
            break;
          case 3:this.moveUp();
            break;
          case 4:this.moveDown()
        }
      }
    }
  }
</script>

<style scoped lang="less">
  .header {
    display: block;
    margin: 0 auto;
    width: 100%;
    text-align: center;
    h1 {
      font-size: 60px;
      font-weight: bold;
    }
    .new-btn {
      width: 100px;
      padding: 10px;
      background: #8f7a66;
      font-family: Arial, Helvetica, sans-serif;
      color: white;
      border-radius: 10px;
      text-decoration: none;
      &:hover {
        background: #9f8b77;
      }
    }
    .score {
      font-size: 25px;
      margin: 20px auto;
    }
  }
  #grid-container{
    width:340px;
    height:340px;
    margin:auto;
    background:#bbada0;
    border-radius:10px;
    position:relative;
    [id$='0']{
      left: 28px;
    }
    [id$='1']{
      left: 106px;
    }
    [id$='2']{
      left: 184px;
    }
    [id$='3']{
      left: 262px;
    }
    [id*='grid-cell-0']{
      top: 28px;
    }
    [id*='grid-cell-1']{
      top: 106px;
    }
    [id*='grid-cell-2']{
      top: 184px;
    }
    [id*='grid-cell-3']{
      top: 262px;
    }
  }
  .grid-cell{
    width:50px;
    height:50px;
    border-radius:6px;
    line-height:50px;
    text-align: center;
    background:#ccc0b3;
    position:absolute;
  }
  .bg4,.bg2{
    background:#eee4da;
  }
  .bg8{
    background: #f26179;
  }
  .bg16{
    background:#f59563 ;
  }
  .bg32{
    background: #f67c5f;
  }
  .bg64{
    background:#f65e36 ;
  }
  .bg128{
    background:#edcf72 ;
  }
  .bg256{
    background:#edcc61 ;
  }
  .bg512{
    background:#9c0 ;
  }
  .bg1024{
    background:#3365a5 ;
  }
  .bg2048{
    background:#09c ;
  }
  .bg4096{
    background: #a6bc;
  }
  .bg8192{
   background: #93c;
  }
  .bg16384{
    background: black;
  }
  .grid-cell-move {
    transition: transform 1s;
  }
</style>
