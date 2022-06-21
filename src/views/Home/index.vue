<template>
  <div id="Vue_Div" class="container" v-cloak>
    <div class="input-group mb-3">
        <div class="input-group-prepend">
            <span class="input-group-text" id="basic-addon1">代辦事項</span>
        </div>
        <input type="text" class="form-control" placeholder="準備要做的任務" aria-label="Username" aria-describedby="basic-addon1" v-model="addToDoItem">
        <div class="input-group-append">
            <button class="btn btn-success" type="button" id="button-addon2" @click="onHandleAddToDoItem">新增</button>
        </div>
    </div>

    <ul class="nav nav-tabs" id="myTab" role="tablist">
        <li class="nav-item">
            <a class="nav-link active" id="Tab1-tab" data-toggle="tab" href="#Tab1" role="tab" aria-controls="Tab1" aria-selected="true">全部</a>
        </li>
        <li class="nav-item">
            <a class="nav-link" id="Tab2-tab" data-toggle="tab" href="#Tab2" role="tab" aria-controls="Tab2" aria-selected="false">進行中</a>
        </li>
        <li class="nav-item">
            <a class="nav-link" id="Tab3-tab" data-toggle="tab" href="#Tab3" role="tab" aria-controls="Tab3" aria-selected="false">已完成</a>
        </li>
    </ul>
    <div class="tab-content" id="myTabContent">
        <!-- tab-pane: Tab1 -->
        <div class="tab-pane fade show active" id="Tab1" role="tabpanel" aria-labelledby="Tab1-tab">
            <div class="content-wrapper">
                <table class="table no-checkbox">
                    <tbody>
                        <tr v-for="data, idx in toDoList" :key="'All_'+idx">
                            <td :class="{'is-finished': data.status === 1}">
                                {{ data.toDoItem }}
                                <template v-if="data.status === 0">(進行中)</template>
                                <template v-else>(已完成)</template>
                            </td>
                            <td>
                                <button type="button" class="close" aria-label="Close" @click="showDelMsg('one', idx)">
                                    <span aria-hidden="true">&times;</span>                                
                                </button>
                            </td>
                        </tr>
                    </tbody>
                </table>
            </div>
        </div>
        <!-- tab-pane: Tab2 -->
        <div class="tab-pane fade show_div" id="Tab2" role="tabpanel" aria-labelledby="Tab2-tab">
            <div class="content-wrapper">
                <table class="table has-checkbox">
                    <tbody>
                        <tr v-for="data, idx in toDoList" :key="'ONDOING_'+idx">
                            <template v-if="data.status === 0">
                                <td>
                                    <input type="checkbox" value="" @click="onHandleChageStatus(idx)">
                                </td>
                                <td>
                                    {{ data.toDoItem + '(進行中)' }}
                                </td>
                                <td>
                                    <button type="button" class="close" aria-label="Close" @click="showDelMsg('one', idx)">
                                        <span aria-hidden="true">&times;</span>                                
                                    </button>
                                </td>
                            </template>
                        </tr>
                    </tbody>
                </table>
            </div>
        </div>
        <!-- tab-pane: Tab3 -->
        <div class="tab-pane fade show_div" id="Tab3" role="tabpanel" aria-labelledby="Tab3-tab">
            <div class="content-wrapper">
                <table class="table no-checkbox">
                    <tbody>
                        <tr v-for="data, idx in toDoList" :key="'FINISHED_'+idx">
                            <template v-if="data.status === 1">
                                <td :class="{'is-finished': data.status === 1}">
                                    {{ data.toDoItem + '(已完成)' }}
                                </td>
                                <td>
                                    <button type="button" class="close" aria-label="Close" @click="showDelMsg('one', idx)">
                                        <span aria-hidden="true">&times;</span>                                
                                    </button>
                                </td>
                            </template>
                        </tr>
                    </tbody>
                </table>
            </div>
        </div>
    </div>
    <!-- foot -->
    <div class="footTool">
        <div>{{ '還有 ' + unFinishedNum + ' 筆任務未完成' }}</div>
        <div class="clear-all-btn" @click="showDelMsg('all', -1)">清除所有任務</div>
    </div>
    <!-- Modal: Message -->
    <div class="modal" id="messageModal" tabindex="-1" role="dialog" aria-labelledby="messageModalTitle" aria-hidden="true">
        <div class="modal-dialog modal-dialog-centered" role="document">
            <div class="modal-content">
                <div class="modal-body" id="messageDiv">
                </div>
                <div class="modal-footer">
                    <button type="button" class="btn btn-success btn-sm" data-dismiss="modal">OK</button>
                </div>
            </div>
        </div>
    </div>
    <!-- Modal: check clear -->
    <div class="modal" id="checkClearModal" tabindex="-1" role="dialog" aria-labelledby="checkClearModallTitle" aria-hidden="true">
        <div class="modal-dialog modal-dialog-centered" role="document">
            <div class="modal-content">
                <div class="modal-header">
                    <h5 class="modal-title text-xl font-bold" id="checkClearModalTitle">清除任務</h5>
                </div>
                <div class="modal-body" id="messageDiv_Clear">
                </div>
                <div class="modal-footer">
                    <button type="button" class="btn btn-secondary btn-sm" data-dismiss="modal">取消</button>
                    <button type="button" class="btn btn-danger btn-sm" data-dismiss="modal" @click="onHandleDeleted">清除</button>
                </div>
            </div>
        </div>
    </div>
  </div>
</template>

<script>
/* eslint-disable */  // 禁用ESLint
export default {
  name: "HomeIndex",
  data() {
    return {
        addToDoItem: '',
        toDoList: [],
        delIdx: -9999,
    };
  },
  created() {
    let $Vue = this;
    window.addEventListener('keyup', function (event) {
        //console.log(event.keyCode)  // enter 13 space 32
        if (event.keyCode === 13) {
            $Vue.onHandleAddToDoItem();
        }
    });
    this.getToDoList();
  },
  beforeDestroy() {
    let $Vue = this;
    window.removeEventListener('keyup', function (event) {
        if (event.keyCode === 13) {
            $Vue.onHandleAddToDoItem();
        }
    });
  },
  computed: {
    unFinishedNum: {
        get() {
            let result = [];
            result = this.toDoList.filter((item) => {
                return item.status !== 1;
            });

            return result.length
        }
    },
  },
  methods: {
    async getToDoList() {
        this.toDoList = [];
        $('#Loading_dialog').modal('show');
        if (localStorage.getItem('toDoListData')) {
            this.toDoList = await JSON.parse(localStorage.getItem('toDoListData'));
        };
        $('#Loading_dialog').modal('hide');
    },
    async onHandleAddToDoItem() {
        $('#Loading_dialog').modal('show');
        if (this.addToDoItem !== "") {            
            let params = JSON.parse(JSON.stringify(this.toDoList));
            params.push({
                status: 0,
                toDoItem: this.addToDoItem
            })
            
            await localStorage.setItem('toDoListData', JSON.stringify(params));
        } else {
            this.showMsg(['請輸入要做的任務']);
        };
        $('#Loading_dialog').modal('hide');
        this.addToDoItem = '';
        this.getToDoList();
    },
    async onHandleChageStatus(idx) {
        $('#Loading_dialog').modal('show');

        let params = JSON.parse(JSON.stringify(this.toDoList));
        params[idx].status = 1;

        await localStorage.setItem('toDoListData', JSON.stringify(params));
        
        $('#Loading_dialog').modal('hide');
        this.getToDoList();
    },
    async onHandleDeleted() {
        $('#Loading_dialog').modal('show');

        if (this.delIdx >= 0) {
            let params = JSON.parse(JSON.stringify(this.toDoList));
            params.splice(this.delIdx, 1);
            await localStorage.setItem('toDoListData', JSON.stringify(params));
        } else if (this.delIdx === -1) {
            await localStorage.removeItem('toDoListData');
        };
        
        $('#Loading_dialog').modal('hide');
        this.delIdx = -9999;
        this.getToDoList();
    },
    showDelMsg(rangeStr, idx) {
        let msgText = '';
        this.delIdx = idx;
        msgText = rangeStr === "all" ? "所有任務" : "任務(" + this.toDoList[idx].toDoItem + ")";

        let msgHtml = "";
        msgHtml += "<p class='font-bold'>確認清除 : " + msgText + "</p>";
        $("#messageDiv_Clear").html(msgHtml);
        $('#checkClearModal').modal('show');
    },
    showMsg(Msg) {
        let msgHtml = "";
        Msg.forEach((item) => {
            msgHtml += "<p class='font-bold'>" + item + "</p>";
        });
        $("#messageDiv").html(msgHtml)
        $('#messageModal').modal('show');
    },
  }
};
</script>

<style scope>
</style>