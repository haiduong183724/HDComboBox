.<template>
    <div class="combo-box-wraper">
        <div class="combo-box-wrap" v-on:click = "()=>{isshow = !isshow }" v-on:change = "search()" :class="{'no-data':!valid}">
            <input type="text" v-model="selectValue.text" v-on:input = "search()">
            <div class="combo-select-show">
                <i class="material-icons">
                    arrow_drop_down
                </i>
            </div>
        </div>
        <div class="selected-list-combo-box" :class="{'selection-show':isshow}" id = 'combo-box-data'>
            <div  v-for="(item) in dataShow" :key="item.value" class="combo-selection" 
            v-on:click = "select(item)" :class="{'item-selected': selectValue.text == item.text}">
                <span>{{item.text}}</span>
            </div>
        </div>
    </div>
</template>
<script>
export default {
    props:{
        width:String,
        height:String,
        selected:Object ,
        data:Array,
    },
    data(){
        return{
            isshow:false,
            selectValue: {},
            valid:true,
            cacheData:[
                {text:'Nam', value:0},
                {text:'Nữ', value:1},
                {text:'Khác', value:2},
            ],
            dataShow:[],
        };
        
    },
    created(){
        let me = this;
        this.dataShow = this.cacheData;
        document.addEventListener('mouseup', function(e) {
        var container = document.getElementById('combo-box-data');
        if (!container.contains(e.target)) {
            me.hideform();
        }
});
    },
    mounted(){
    
    },
    methods:{
        hideform(){
            this.isshow = false;
        },
        select(value){
            this.selectValue = {...value};
            this.isshow = false;
            console.log(this.cacheData);
        },
        fomatText(str){
            str = str.replace(/à|á|ạ|ả|ã|â|ầ|ấ|ậ|ẩ|ẫ|ă|ằ|ắ|ặ|ẳ|ẵ/g,"a"); 
            str = str.replace(/è|é|ẹ|ẻ|ẽ|ê|ề|ế|ệ|ể|ễ/g,"e"); 
            str = str.replace(/ì|í|ị|ỉ|ĩ/g,"i"); 
            str = str.replace(/ò|ó|ọ|ỏ|õ|ô|ồ|ố|ộ|ổ|ỗ|ơ|ờ|ớ|ợ|ở|ỡ/g,"o"); 
            str = str.replace(/ù|ú|ụ|ủ|ũ|ư|ừ|ứ|ự|ử|ữ/g,"u"); 
            str = str.replace(/ỳ|ý|ỵ|ỷ|ỹ/g,"y"); 
            str = str.replace(/đ/g,"d");
            str = str.replace(/À|Á|Ạ|Ả|Ã|Â|Ầ|Ấ|Ậ|Ẩ|Ẫ|Ă|Ằ|Ắ|Ặ|Ẳ|Ẵ/g, "A");
            str = str.replace(/È|É|Ẹ|Ẻ|Ẽ|Ê|Ề|Ế|Ệ|Ể|Ễ/g, "E");
            str = str.replace(/Ì|Í|Ị|Ỉ|Ĩ/g, "I");
            str = str.replace(/Ò|Ó|Ọ|Ỏ|Õ|Ô|Ồ|Ố|Ộ|Ổ|Ỗ|Ơ|Ờ|Ớ|Ợ|Ở|Ỡ/g, "O");
            str = str.replace(/Ù|Ú|Ụ|Ủ|Ũ|Ư|Ừ|Ứ|Ự|Ử|Ữ/g, "U");
            str = str.replace(/Ỳ|Ý|Ỵ|Ỷ|Ỹ/g, "Y");
            str = str.replace(/Đ/g, "D");
            // Some system encode vietnamese combining accent as individual utf-8 characters
            // Một vài bộ encode coi các dấu mũ, dấu chữ như một kí tự riêng biệt nên thêm hai dòng này
            str = str.replace(/\u0300|\u0301|\u0303|\u0309|\u0323/g, ""); // ̀ ́ ̃ ̉ ̣  huyền, sắc, ngã, hỏi, nặng
            str = str.replace(/\u02C6|\u0306|\u031B/g, ""); // ˆ ̆ ̛  Â, Ê, Ă, Ơ, Ư
            // Remove extra spaces
            // Bỏ các khoảng trắng liền nhau
            str = str.replace(/ + /g," ");
            str = str.trim();
            // Remove punctuations
            // Bỏ dấu câu, kí tự đặc biệt
            str = str.replace(/Ư|@|%|\^|\*|\(|\)|\+|=|<|>|\?|\/|,|\.|:|;|'|"|&|#|\[|\]|~|\$|_|`|-|{|}|\||\\/g," ");
            return str;
        },
        search(){
            let KW = this.selectValue.text.toLowerCase();
            let value = this.fomatText(KW);
            console.log(value);
            this.dataShow = [];
            if(value != undefined){
                this.isshow = true;
                console.log("a");
                for (let index = 0; index < this.cacheData.length; index++) {
                    const text = this.fomatText(this.cacheData[index].text.toLowerCase());
                    if(text.search(value.toLowerCase()) != -1){
                        this.dataShow.push(this.cacheData[index]);
                    }
                }
            }
        }
    },
    updated(){
        if(this.selectValue.text == ''){
            this.dataShow = this.cacheData;
        }

    }
}
</script>

<style scoped>
@import url("https://fonts.googleapis.com/icon?family=Material+Icons");
    *{
        padding: 0;
        margin: 0;
    }
    .combo-box-wraper{
        max-height: 100px;
        min-height: 40px;
        min-width: 100px;
        max-width: 300px;
    }
    .combo-box-wrap{
        display: flex;
        border: 1px solid gray;
        align-items: center;
        box-sizing: border-box;
    }
    .selection-show{
        display: block !important;  
    }
    .no-data{
        border: 1px red;
    }
    .combo-box-wrap input[type="text"]{
        border: none;
        outline: 0;
        width: 100%;
        padding: 5px;
        height: calc(100% - 10px);
        font-size:16px ;
    }
    .combo-box-wrap .combo-select-show{
        padding:5px ;
        cursor: pointer;
        width: 40px;
    }
    .selected-list-combo-box{
        text-align: left;
        border: 1px solid rgb(104, 102, 102);
        display: none;
        border-top:none;
    }
    .selected-list-combo-box .combo-selection{
        padding: 5px;
    }
    .selected-list-combo-box .combo-selection:hover{
        background:rgb(61, 46, 46);
        color: white;
    }
    .item-selected{
        background: #8ec6ff;
    }
</style>