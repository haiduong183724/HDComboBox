.<template>
    <div class="combo-box-wraper" id="hd-combo-box" v-on:keyup.38 = "moveUp()" 
        v-on:keyup.40 = "moveDown()" v-on:keyup.enter = "selectedByEnter()" >
        <div class="combo-box-wrap" v-on:change = "search()" :class="{'no-data':!valid ,'active':isactive}" :style="[widthLength, heightLength]">
            <input type="text" v-model="selectValue.text" v-on:input = "search()" ref = "value">
            <div class="combo-select-show" v-on:click = "toggleSelect()">
                <i class="material-icons">
                    arrow_drop_down
                </i>
            </div>
        </div>
        <div class="selected-list-combo-box" :class="{'selection-show':isshow,}" id = 'combo-box-data' :style="widthLength">
            <div  v-for="(item, index) in dataShow" :key="item.value" class="combo-selection" 
            v-on:click = "select(item)" :class="{'item-selected': selectValue.text == item.text, 'row-current':index == rowMarked}">
                <div class="ticker-icon">
                <div class="icon-wrap" :class="{'selection-show': item.text == selectValue.text}">
                    <i class="material-icons" style="font-size:20px">
                        done
                    </i>
                </div>
                </div>
                <div class="select-text">
                    <span>{{item.text}}</span>
                </div>
            </div>
        </div>
    </div>
</template>
<script>
export default {
    props:{
        width:Number,
        height:Number,
        // thông tin của các select trong combo box
        data:Array,
    },
    data(){
        return{
            // isshow = true:  hiển thị selecbox, isshow = false: đóng 
            isshow:false,
            // giá trị đang được lựa chọn của selecbox
            selectValue: {},
            // giá trị của selectbox có hợp lệ hay không
            valid:true,
            // mảng lưu các giá trị select được truyền vào từ component cha
            cacheData:this._props.data,
            // mảng lưu các thông tin sẽ được hiển thị ta màn hình
            dataShow:[],
            // giá trị của dòng đang được đánh dấu trong combobox
            rowMarked:-1,
            comboboxId:this._props.id,
            isactive:false,
            widthLength:{
                width: `${this._props.width}px`,
                
            },
            heightLength:{
                height: `${this._props.height}px`,
            }
            
        };
        
    },
    //khởi tạo 
    created(){
        this.dataShow = this.cacheData;
    },
    mounted(){
        let me = this;
        // đóng combo box khi nhấp ra ngoài màn hình
        document.addEventListener('mouseup', function(e) {
        var container = document.getElementById('hd-combo-box');
        if (!container.contains(e.target)) {
            me.hideform();
            me.isactive = false;
            if(me.selectValue.text != undefined && me.selectValue.text != '') {
                me.checkValue();
            }
        }
        else{
            me.isactive = true;
        }
        });
        this.$el.getValue = function(){return this.value;}
        this.$el.getText = function(){return this.text;}
    },
    methods:{
        // hàm xử lý khi nhấn nút xuống
        moveDown(){
            let me = this;
            // chuyển dòng đang được đánh dấu lên trên
            if(me.rowMarked < this.dataShow.length -1){
                this.rowMarked++;
                console.log("a");
            }
        },
        // hàm xử lý khi nhấn nút lên
        moveUp(){
            let me = this;
            // chuyển dòng đang được đánh dấu lên trên
            if(me.rowMarked > 0){
                this.rowMarked--;
            }
            
        },
        toggleSelect(){
            this.dataShow = this.cacheData;
            this.isshow=!this.isshow;
            this.$refs.value.focus();
        },
        // hàm xử lý khi nhấn enter
        selectedByEnter(){
            let me = this;
            // nếu có 1 dòng đã được đánh dấu
            if(me.rowMarked>=0 && me.rowMarked < me.dataShow.length){
                let currentValue = me.dataShow[me.rowMarked];
                // giá trị của combobox là giá trị dòng đánh dấu đó
                me.select(currentValue);
                me.rowMarked = -1;
                this.valueValid();
            }
            // chưa có dòng nào dược đánh dấu
            else{
                // nếu có ít nhất 1 giá trị khớp với text trong combobox
                if(me.dataShow.length >= 1){
                    // lấy luôn giá trị đầu tiên làm giá trị cho combobox
                    let currentValue = me.dataShow[0];
                    me.select(currentValue);
                    me.valueValid();
                }
                else{
                    // nếu không có giá trị nào khớp: đóng form, hiện invalid
                    me.hideform();
                    me.valueInvalid();
                }
            }
        },
        getValue(){
            return this.selectValue.value;
        },
        getText(){
            return this.selectValue.text;
        },
        // ẩn form đi
        hideform(){
            this.isshow = false;
        },
        // lựa chọn 1 giá trị
        select(value){
            this.selectValue = {...value};
            this.hideform();
            this.valueValid();
            this.$el.value = this.selectValue.value;
            this.$el.text = this.selectValue.text;
        },
        // chuyển từ tiếng việt có dấu sang không dấu
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
            // Một vài bộ encode coi các dấu mũ, dấu chữ như một kí tự riêng biệt nên thêm hai dòng này
            str = str.replace(/\u0300|\u0301|\u0303|\u0309|\u0323/g, ""); // ̀ ́ ̃ ̉ ̣  huyền, sắc, ngã, hỏi, nặng
            str = str.replace(/\u02C6|\u0306|\u031B/g, ""); // ˆ ̆ ̛  Â, Ê, Ă, Ơ, Ư
            // Bỏ các khoảng trắng liền nhau
            str = str.replace(/ + /g," ");
            str = str.trim();
            // Bỏ dấu câu, kí tự đặc biệt
            str = str.replace(/Ư|@|%|\^|\*|\(|\)|\+|=|<|>|\?|\/|,|\.|:|;|'|"|&|#|\[|\]|~|\$|_|`|-|{|}|\||\\/g," ");
            return str;
        },
        // tìm kiếm các giá trị khớp với text trong combobox
        search(){
            // chuyển từ khóa tìm kiếm về chữ thường
            let value = this.selectValue.text.toLowerCase();
            this.dataShow = [];
            if(value != undefined){
                this.isshow = true;
                // tìm kiếm các giá trị match với giá trị cần tìm
                for (let index = 0; index < this.cacheData.length; index++) {
                    // chuyển các text sang tiếng việt không dấu
                    const text = this.fomatText(this.cacheData[index].text.toLowerCase());
                    if(text.search(value.toLowerCase()) != -1){
                        this.dataShow.push(this.cacheData[index]);
                    }
                }
            }
            
        },
        valueInvalid(){
            this.valid = false;
            this.$el.firstElementChild.setAttribute("title", 'Trường thông tin nhập không hợp lệ')
        },
        valueValid(){
            this.$el.firstElementChild.removeAttribute('title');
            this.valid = true;
        },
        checkValue(){
            let valueCheck = this.selectValue.text;
            for (let index = 0; index < this.dataShow.length; index++) {
                let currentText = this.dataShow[index].text;
                let currentValue = this.dataShow[index];
                if(valueCheck == currentText){
                    this.selectValue = {...currentValue};
                    this.valueValid();
                    return;
                }
            }
            this.valueInvalid();
        },
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
        border: 2px solid red;
    }
    .combo-box-wrap input[type="text"]{
        border: none;
        outline: 0;
        width: 100%;
        padding: 5px;
        font-size:16px ;
    }
    .combo-box-wrap .combo-select-show{
        padding:5px ;
        cursor: pointer;
        width: calc(height);
        border-left:1px solid rgb(104, 102, 102);
    }
    .selected-list-combo-box{
        text-align: left;
        border: 1px solid rgb(104, 102, 102);
        display: none;
        border-top:none;
        z-index: 10000;
        position: absolute;
        box-sizing: border-box;
        max-height: 100px;
        min-height: 40px;
        min-width: 100px;
        max-width: 300px;
    }
    .selected-list-combo-box .combo-selection{
        padding: 5px;
        display: flex;
        align-items: center;
    }
    .selected-list-combo-box .combo-selection:hover{
        background:rgb(61, 46, 46);
        color: white;
    }
    .item-selected{
        background: #09610e;
        color: rgb(255, 255, 255);
    }
    .row-current{
        background: rgb(112, 184, 202) !important ;
    }
    .active{
        border: 2px solid rgb(53, 202, 53) !important;
    }
    .ticker-icon{
        height: 100%;
        width: 30px;
    }
    .icon-wrap{
        display: none;
    }
    .select-text{
        width: 100%;
        text-align: left;
    }
</style>