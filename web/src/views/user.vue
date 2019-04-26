<template>
 <div style="margin-bottom: 20px;">
   <div>
     <Row style="margin-bottom: 25px;">
       <Col span="8">登录名：
         <Input v-model="loginName" placeholder="请输入..." style="width:200px"></Input>
       </Col>
       <Col span="8"><Button type="primary" shape="circle" icon="ios-search" @click="search()">搜索</Button></Col>
     </Row>
   </div>
   <div>
     <ul>
       <li>
         <Button type="primary" icon="md-add" @click="openNewModal()">新建</Button>
         <Button type="success" icon="md-build" @click="">修改</Button>
         <Button type="error" icon="md-trash" @click="">删除</Button>
       </li>
       <li>
         <div style="padding: 10px 0;">
           <Table border :columns="columns1" :data="data1" :height="400" @on-selection-change="s=>{change(s)}" @on-row-dblclick="s=>{dblclick(s)}"></Table>
         </div>
       </li>
       <li>
         <div style="text-align: right;">
           <Page :total="total" :page-size="pageInfo.pageSize" show-elevator show-total @on-change="e=>{pageSearch(e)}"></Page>
         </div>
       </li>
     </ul>
   </div>
 </div>
</template>

<script>
    export default {
        name: "user",
      data () {
        return {
          /*用于查找的登录名*/
          LoginName:null,
          /*选择的数量*/
          count:null,
          /*选中的组数据*/
          groupId:null,
          /*新建modal的显示参数*/
          newModal:false,
          /*修改modal的显示参数*/
          modifyModal:false,
          /*角色配置modal的显示参数*/
          roleModal:false,
          /*分页total属性绑定值*/
          total:0,
          /*loading*/
          loading: true,
          /*pageInfo实体*/
          pageInfo:{
            page:0,
            pageSize:10
          },
          /*user实体*/
          user:{
            id:null,
            name:null,
            loginName:null,
            password:null,
            passwordAgain:null,
            email:null
          },
          /*用于添加的user实体*/
          userNew:{
            id:null,
            name:null,
            loginName:null,
            password:null,
            passwordAgain:null,
            email:null
          },
          /*用于修改的user实体*/
          userModify:{
            id:null,
            name:null,
            loginName:null,
            password:null,
            email:null
          },
          /*新建验证*/
          ruleNew:{
            name: [
              { type:'string',required: true, message: '输入用户名', trigger: 'blur' }
            ],
            loginName: [
              { type:'string',required: true, message: '输入登录名', trigger: 'blur' }
            ],
            password: [
              { type:'string',required: true, message: '输入密码', trigger: 'blur' }
            ],
            passwordAgain: [
              { type:'string',required: true, message: '输入再次密码', trigger: 'blur' }
            ],
            email: [
              { required: true, message: '输入邮箱', trigger: 'blur' },
              { type:'email', message: '输入正确的邮箱格式', trigger: 'blur' }
            ]
          },
          /*修改验证*/
          ruleModify:{
            name: [
              { type:'string',required: true, message: '输入用户名', trigger: 'blur' }
            ],
            loginName: [
              { type:'string',required: true, message: '输入登录名', trigger: 'blur' }
            ],
            password: [
              { type:'string',required: true, message: '输入密码', trigger: 'blur' }
            ],
            email: [
              { required: true, message: '输入邮箱', trigger: 'blur' },
              { type:'email', message: '输入正确的邮箱格式', trigger: 'blur' }
            ]
          },
          /*表显示字段*/
          columns1: [
            {
              type: 'selection',
              width: 60,
              align: 'center'
            },
            {
              title: '登录名',
              key: 'loginName'
            },
            {
              title: '用户名',
              key: 'name'
            },
            {
              title: '邮箱',
              key: 'email'
            },
            {
              title: '操作',
              align: 'center',
              key: 'action',
              render: (h, params) => {
                return h('div', [
                  h('Button', {
                    props: {
                      type: 'info',
                    },
                    on: {
                      click: () => {
                        this.relationSet(params.row);
                      }
                    }
                  },'配置角色')
                ]);
              }
            },
          ],
          /*表数据*/
          data1: [],
          /*表显示字段*/
          columns2: [
            {
              type: 'selection',
              width: 60,
              align: 'center'
            },
            {
              title: '角色名称',
              width: 120,
              key: 'name'
            },
            {
              title: '描述',
              key: 'describe'
            }
          ],
          /*表数据*/
          data2:[],
          /*data2的临时存储*/
          data2Temp:[],
          /*用户与角色关系列表*/
          relationList:null
        }
      },
      mounted() {
      },
      methods:{
        /*pageInfo实体初始化*/
        initPageInfo(){
          this.pageInfo.page = 0;
          this.pageInfo.pageSize = 10;
        },
        /*user实体初始化*/
        initUser(){
          this.user.id = null;
          this.user.name = null;
          this.user.loginName = null;
          this.user.password = null;
          this.user.email = null;
        },
        /*userNew实体初始化*/
        initUserNew(){
          this.userNew.id = null;
          this.userNew.name = null;
          this.userNew.loginName = null;
          this.userNew.password = null;
          this.userNew.passwordAgain = null;
          this.userNew.email = null;
        },
        /*userModify实体初始化*/
        initUserModify(){
          this.userModify.id = null;
          this.userModify.name = null;
          this.userModify.loginName = null;
          this.userModify.password = null;
          this.userModify.email = null;
        },
        /*userNew设置*/
        userSet(e){
          this.user.id = e.id;
          this.user.name = e.name;
          this.user.loginName = e.loginName;
          this.user.password = e.password;
          this.user.email = e.email;
        },
        /*userNew设置*/
        userNewSet(e){
          this.userNew.id = e.id;
          this.userNew.name = e.name;
          this.userNew.loginName = e.loginName;
          this.userNew.password = e.password;
          this.userNew.passwordAgain = e.password;
          this.userNew.email = e.email;
        },
        /*userModify设置*/
        userModifySet(e){
          this.userModify.id = e.id;
          this.userModify.name = e.name;
          this.userModify.loginName = e.loginName;
          this.userModify.password = e.password;
          this.userModify.email = e.email;
        },
      }
    }
</script>

<style scoped>

</style>
