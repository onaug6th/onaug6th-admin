<template>
    <div>
        <p class="alert top-alert">楼中楼回复管理<br />
            <font size="1">文章的楼中楼回复管理</font>
        </p>
        <div class="row">
            <div class="col-sm-12">
                <div class="portlet">
                    <div class="portlet-title">
                        <div class="caption" style="color:#32c5d2;">
                            <i class="glyphicon glyphicon-user"></i>
                            <span> 文章楼中楼回复管理 </span>
                        </div>
                    </div>
                    <div>
                        <superOperate :config="operateConfig"></superOperate>
                        <superTable ref="superTable" :config="tableConfig" @pageChange="getInsideReplyList"></superTable>
                    </div>
                </div>
            </div>
        </div>
        <superModal :config="modalConfig"></superModal>
    </div>
</template>

<script>
import superOperate from "@/components/superOperate.vue";
import superTable from "@/components/superTable.vue";
import superModal from "@/components/superModal.vue";

export default {
    name: 'articleInsideReplyManage',
    components: {
        superOperate,
        superTable,
        superModal
    },
    data(){
        return {
            tableConfig : {},
            operateConfig : {},
            modalConfig : {}
        }
    },
    mounted(){
        this.setTableConfig();
        this.setOperateConfig();
        this.setModalConfig();
        this.getInsideReplyList();
    },
    computed:{

    },
    methods : {
        //  设置超级表格组件配置
        setTableConfig(){
            this.tableConfig = {
                checkbox : true,
                clickToSelect : true,
                colOption : [
                    {
                        field : "id",
                        label : "ID"
                    },
                    {
                        field : "floor",
                        label : "所属楼层"
                    },
                    {
                        field : "replyId",
                        label : "回复目标的ID"
                    },
                    {
                        field : "replyName",
                        label : "回复目标的名称"
                    },
                    {
                        field : "ip",
                        label : "回复人ip"
                    },
                    {
                        field : "name",
                        label : "回复人名称"
                    },
                    {
                        field : "email",
                        label : "回复人邮箱"
                    },
                    {
                        field : "content",
                        label : "回复内容"
                    },
                    {
                        field : "reply",
                        label : "回复次数"
                    },
                    {
                        field : "like",
                        label : "点赞"
                    },
                    {
                        field : "show",
                        label : "是否显示"
                    },
                    {
                        field : "operate",
                        label : "操作",
                        jsxRender: (h, params) =>{
                            return (
                                <div>
                                    <button 
                                        class="btn btn-primary" 
                                        onClick={() => {
                                            this.openModal(params.row);
                                        }}
                                        >编辑</button>
                                </div>
                            )
                        }
                    }
                ],
                data : [],
                pagination : {
                    prevText : "前页",
                    nextText : "后页",
                    page : 1,
                    pageSize : 10,
                    totalPages : 0
                }
            }
        },
        //  设置操作区域组件配置
        setOperateConfig(){
            this.operateConfig = {
                list : [
                    {
                        type : "dropdown",
                        text : "删除操作",
                        list : [
                            {
                                text : "删除文章回复",
                                events : {
                                    onClick : {
                                        fn : this.deleteArticleReply,
                                        params : [this.tableConfig]
                                    }
                                }
                            }
                        ]
                    }
                ]
            }
        },
        //  设置超级模态框组件配置
        setModalConfig(){
            this.modalConfig = {
                title : "编辑文章回复",
                backdrop : "static",
                show : false,
                form : {
                    config : [
                        {
                            labelText : "ID",
                            field : "id",
                            type : "text"
                        },
                        {
                            labelText : "所属楼层",
                            field : "floor",
                            type : "text"
                        },
                        {
                            labelText : "回复的文章的ID",
                            field : "replyId",
                            type : "text"
                        },
                        {
                            labelText : "回复目标的名称",
                            field : "replyName",
                            type : "text"
                        },
                        {
                            labelText : "回复人IP",
                            field : "ip",
                            type : "text"
                        },
                        {
                            labelText : "回复人名称",
                            field : "name",
                            type : "text"
                        },
                        {
                            labelText : "回复人邮箱",
                            field : "email",
                            type : "text"
                        },
                        {
                            labelText : "回复人头像",
                            field : "avatar",
                            type : "text"
                        },
                        {
                            labelText : "回复内容",
                            field : "content",
                            type : "text"
                        },
                        {
                            labelText : "回复次数",
                            field : "reply",
                            type : "text"
                        },
                        {
                            labelText : "点赞",
                            field : "like",
                            type : "text"
                        },
                        {
                            labelText : "是否显示",
                            field : "show",
                            type : "text"
                        }
                    ],
                    data : {
                        id : "",
                        floor : "",
                        replyId : "",
                        replyName : "",
                        ip : "",
                        name : "",
                        email : "",
                        avatar : "",
                        content : "",
                        reply : "",
                        like : "",
                        show : ""
                    }
                },
                footer: {
                    textalign : "center",
                    btnList : [
                        {
                            id : "edit",
                            show : true,
                            type : "primary",
                            text : "修改",
                            events : {
                                onClick : {
                                    fn : this.commonHandle,
                                    params : ["edit"]
                                }
                            }
                        }
                    ]
                }
            }
        },
        /**
         * 打开编辑页面
         * @param {object} item 行对象
         */
        openModal(item){

            const modal = this.modalConfig;

            for(let i in item){
                (modal.form.data[i] = item[i]);
            }

            modal.show = true;
        },
        /**
         * 通用操作，处理模态框新增和编辑动作
         * @param {object} modalConfig 模态框配置
         * @param {string} type 类型
         */
        commonHandle(modalConfig, type){
            const that = this;
            const formData = modalConfig.form.data;
            let method = "post"
            let url = "insideReply";

            type == "edit" && (method = "put", url += `/${formData.id}`);

            that.$http[method](url, formData)
                .then((result) =>{
                    this.$swal(result.detailMsg);
                    modalConfig.show = false;
                    that.getInsideReplyList();
                });

        },
        //  获取文章类型列表
        getInsideReplyList(){

            this.$http({
                url: 'insideReply/list',
                method: 'post',
                data : {
                    page : this.tableConfig.pagination.page,
                    pageSize : this.tableConfig.pagination.pageSize,
                    type : "admin"
                }
            }).then((result) =>{
                    this.tableConfig.data = result.data.rows;
                    this.tableConfig.pagination.totalPages = result.data.totalPages
                });

        },
        /**
         * 删除文章楼中楼回复
         * @param {object} item 组件传回来的我也忘了是啥
         */
        deleteArticleReply(item){
            const checkedData = this.$refs.superTable.checkedData.map((item, index)=>{
                return item["id"]
            });

            if(!checkedData.length){
                return this.$swal("请选择要操作的数据");
            }

            this.$http.delete("insideReply", {
                    data : {
                        arr : checkedData
                    }
                })
                .then((result) =>{
                    this.$swal(result.detailMsg);
                    this.getInsideReplyList();
                });
                
        }
    }
}
</script>
