<template>
  <div class="main">
    <div class="topTool">
      <div id="xd-tool-bar">
        <!-- 左侧按钮 -->
        <!-- 右侧搜索 -->
                <div class="block" style="float: left;padding: 5px;">
          <el-select v-model="value" placeholder="是否启用" size="small">
            <el-option :label="'是'" :value="'是'"></el-option>
            <el-option :label="'否'" :value="'否'"></el-option>
          </el-select>
        </div>
        <div style="float: left;padding: 5px;">
          <el-input placeholder="请输入内容" v-model="input5" class="input-with-select" size="small">
            <el-select class="select-ds" v-model="select1" slot="prepend" placeholder="地市">
              <el-option :label="item.DWMC" :value="item.DWMC" v-for="item in ds" :key="item.ID"></el-option>
            </el-select>
            <el-select class="select-szqy" v-model="select2" slot="prepend" placeholder="所在区域">
              <el-option :label="item.DWMC" :value="item.DWMC" v-for="item in szqy" :key="item.ID"></el-option>
            </el-select>
            <el-button slot="append" icon="el-icon-search" @click="search"></el-button>
          </el-input>
        </div>
        <div style="float: left;padding: 5px;">
         <el-button  type="primary" size="small" @click="value ='';select1 = '';select2 = '' ">重置</el-button>
        </div>
        <div style="float: left;padding: 5px;">
          <el-button type="primary" size="small" @click="addList()">新增</el-button>
          <el-button type="primary" size="small" @click="exportExcel">导出excel</el-button>
        </div>

      </div>
    </div>
    <div class="flex-table">
      <div class="flex-table-wrap" style>
        <el-table
          v-loading="loading"
          ref="multipleTable"
          :data="tableData3"
          height="100%"
          id="out-table-1"
        >
          <!-- tooltip-effect="dark" -->
          <!-- <el-table-column type="selection" width="100px"></el-table-column> -->
          <el-table-column label="序号" width="100" align="center">
            <template slot-scope="scope">
              <span>{{scope.$index+1}}</span>
            </template>
          </el-table-column>
          <el-table-column prop="DS" label="地市" show-overflow-tooltip></el-table-column>
          <el-table-column prop="SZQY" label="所在区域" width="100" show-overflow-tooltip></el-table-column>
          <el-table-column prop="LXBM" label="路线编码" width="120" show-overflow-tooltip></el-table-column>
          <el-table-column prop="LXMC" label="路线名称" width="200" show-overflow-tooltip></el-table-column>
          <el-table-column prop="YLSJJWZ" label="与邻省交界位置" width="200" show-overflow-tooltip></el-table-column>
          <el-table-column prop="JWD" label="经纬度" width="200" show-overflow-tooltip></el-table-column>
          <el-table-column prop="ZDMC" label="站点名称" width="200" show-overflow-tooltip></el-table-column>
          <el-table-column prop="FBZT" label="封闭状态" width="200" show-overflow-tooltip></el-table-column>
          <el-table-column prop="SFQY" label="是否启用" width="200" show-overflow-tooltip></el-table-column>
          <el-table-column prop="TBSL" label="填报数量" width="200" show-overflow-tooltip></el-table-column>
          <el-table-column label="操作" width="180" fixed="right"  ref="excel" >
            <template slot-scope="scope"  ref="excel">
              <el-button type="text" size="small" @click="edit(scope.row)">编辑</el-button>
              <el-button
                type="text"
                size="small"
                @click="id = scope.row.ID ;centerDialogVisible = true"
              >删除</el-button>
              <el-button type="text" size="small" @click="addStatistic(scope.row)">新增统计信息</el-button>
            </template>
          </el-table-column>
        </el-table>
      </div>
    </div>
    <div class="pop">
      <el-dialog title="请填写数据" :visible.sync="dialogForm">
        <el-form :model="form">
          <el-form-item label="地市" :label-width="formLabelWidth">
            <el-select v-model="form.DS" placeholder="请选择地市">
              <el-option
                :label="item.DWMC"
                :value="item.DWMC"
                v-for="item in formds"
                :key="item.ID"
              ></el-option>
            </el-select>
          </el-form-item>
          <el-form-item label="所在区域" :label-width="formLabelWidth">
            <el-select v-model="form.SZQY" placeholder="请选择所在区域">
              <el-option
                :label="item.DWMC"
                :value="item.DWMC"
                v-for="item in formszqy"
                :key="item.ID"
              ></el-option>
            </el-select>
          </el-form-item>
          <el-form-item label="路线编码" :label-width="formLabelWidth">
            <el-input v-model="form.LXBM" autocomplete="off"></el-input>
          </el-form-item>
          <el-form-item label="路线名称" :label-width="formLabelWidth">
            <el-input v-model="form.LXMC" autocomplete="off"></el-input>
          </el-form-item>
          <el-form-item label="与邻省交界位置" :label-width="formLabelWidth">
            <el-input v-model="form.YLSJJWZ" autocomplete="off"></el-input>
          </el-form-item>
          <el-form-item label="原始坐标" :label-width="formLabelWidth">
            <el-input v-model="form.JWD84" autocomplete="off"></el-input>
          </el-form-item>
          <el-form-item label="封闭状态" :label-width="formLabelWidth">
            <el-select v-model="form.FBZT" placeholder="请选择">
              <el-option :label="'未封闭'" :value="'未封闭'"></el-option>
              <el-option :label="'限制通行'" :value="'限制通行'"></el-option>
              <el-option :label="'已封闭'" :value="'已封闭'"></el-option>
            </el-select>
          </el-form-item>
          <!-- <el-form-item label="填报数量" :label-width="formLabelWidth">
            <el-input v-model="form.TBSL" autocomplete="off"></el-input>
          </el-form-item> -->
          <el-form-item label="站点名称" :label-width="formLabelWidth">
            <el-input v-model="form.ZDMC" autocomplete="off"></el-input>
          </el-form-item>
          <el-form-item label="是否启用" :label-width="formLabelWidth">
            <el-select v-model="form.SFQY" placeholder="请选择">
              <el-option :label="'是'" :value="'是'"></el-option>
              <el-option :label="'否'" :value="'否'"></el-option>
            </el-select>
            <!-- <el-input v-model="form.SFQY" autocomplete="off"></el-input> -->
          </el-form-item>
        </el-form>
        <div slot="footer" class="dialog-footer">
          <el-button @click="appname('cancel')">取 消</el-button>
          <el-button type="primary" @click="appname('ok_add')">确 定</el-button>
        </div>
      </el-dialog>
    </div>
            <OrdinaryForm :formds="formds" :formszqy="formszqy" :form="form_statistics" :visible="dialogForm_statistic" @appname="appname_statistic" :formtype="formtype_statistic" />
    <div class="deletePop">
      <el-dialog title="提示" :visible.sync="centerDialogVisible" width="30%" center>
        <span style="text-align:center">请确认是否删除该条数据</span>
        <span slot="footer" class="dialog-footer">
          <el-button @click="centerDialogVisible = false">取 消</el-button>
          <el-button type="primary" @click="ok">确 定</el-button>
        </span>
      </el-dialog>
    </div>
  </div>
</template>
<script>
import FileSaver from "file-saver";
import XLSX from "xlsx";
import OrdinaryForm from "@/components/form/OrdinaryForm.vue"

export default {
  name: "ordinarybasic",
      components:{
OrdinaryForm
  },
  data() {
    return {
      centerDialogVisible: false,
      id: null,
      input5: "",
      select1: "",
      select2: "",
      ds: [], //地市
      formds: [], //form地市
      szqy: [], //所在区域
      formszqy: [], //form所在区域
      tableData3: [],
      loading: true,
      cityData: [],
      formtype: "add", //表单的类型（新增 or 编辑）
      dialogForm: false,
      dialogForm_statistic: false,
      formtype_statistic:'add',
      value:"",
      form: {
        DS: "",
        SZQY: "",
        LXBM: "",
        LXMC: "",
        YLSJJWZ: "",
        JWD84: "",
        ZDMC: "",
        SFQY: "是",
        FBZT: "未封闭",
        TBSL: ""
      },
      form_plac: {
        DS: "",
        SZQY: "",
        LXBM: "",
        LXMC: "",
        YLSJJWZ: "",
        JWD84: "",
        ZDMC: "",
        SFQY: "是",
        FBZT: "未封闭",
        TBSL: ""
      },
            form_statistics: {
        DS: "",
        SZQY: "",
        LXBM: "",
        LXMC: "",
        YLSJJWZ: "",
        SQSL: "",
        SQWZ: "",
        SZBM: "",
        SSNR: "",
        WSQYY: "",
        TJDWLXRXM: "",
        TJDWLXRDH: "",
        SQXCLXRXM: "",
        SQXCLXRDH: "",
        TRJCRYS: "",
        JCCLS: "",
        JCRS: "",
        FSRS: "",
        LGRS: "",
        JCQKSM: "",
        BZ: "",
        TBRQ: "",
        SBZT: "未上报",
        TPDZ: "",
        ZDMC: "",
        FBZT: "未封闭"
      },
                  form_statistics_plac:{
        DS: "",
        SZQY: "",
        LXBM: "",
        LXMC: "",
        YLSJJWZ: "",
        SQSL: "",
        SQWZ: "",
        SZBM: "",
        SSNR: "",
        WSQYY: "",
        TJDWLXRXM: "",
        TJDWLXRDH: "",
        SQXCLXRXM: "",
        SQXCLXRDH: "",
        TRJCRYS: "",
        JCCLS: "",
        JCRS: "",
        FSRS: "",
        LGRS: "",
        JCQKSM: "",
        BZ: "",
        TBRQ: "",
        SBZT: "未上报",
        TPDZ: "",
        ZDMC: "",
        FBZT: "未封闭"
      },
      formLabelWidth: "120px"
    };
  },
  methods: {
    search() {
      this.loading = true;
      this.$post(
          `api/PTGXSJCRK/GetPtgxsjcrkjcAllList?ds=${encodeURI(
            this.select1
          )}&szqy=${encodeURI(this.select2)}&sfqy=${encodeURI(this.value)}`
        )
        .then(res => {
          if (res.data.Message == "OK") {
            this.loading = false;
            let data = JSON.parse(res.data.Data);
            this.tableData3 = data;
          }
        });
    },
    exportExcel() {
      let fix = document.querySelector(".el-table__fixed-right");
      // let caozuo = document.querySelector(".el-table_6_column_79");
      let wb;
      if (fix) {
        //判断要导出的节点中是否有fixed的表格，如果有，转换excel时先将该dom移除，然后append回去

            let dm = document.querySelectorAll('#out-table-1 .el-table__fixed-body-wrapper .el-table__body tbody tr')
            let dm2 = document.querySelectorAll('#out-table-1 .el-table__fixed-header-wrapper .el-table__header thead tr')
      let dmlist = []
      let dm2list = []
                  for(let i=0;i<dm.length;i++){
        if(dm[i].lastChild){
                  dmlist.push(dm[i].lastChild)
        dm[i].removeChild(dm[i].lastChild)
        }
      }
            for(let i=0;i<dm2.length;i++){
        if(dm2[i].lastChild){
                  dm2list.push(dm2[i].lastChild)
        dm2[i].removeChild(dm2[i].lastChild)
        }
      }
      // dm.forEach(v=>{
      //   if(v.lastChild){
      //   dmlist.push(v.lastChild)
      //   v.removeChild(v.lastChild)
      //   }
      // })
      //       dm2.forEach(v=>{
      //   if(v.lastChild){
      //   dm2list.push(v.lastChild)
      //   v.removeChild(v.lastChild)
      //   }
      // })
        wb = XLSX.utils.table_to_book(
          document.querySelector("#out-table-1").removeChild(fix)
        );
                      for(let i=0;i<dm.length;i++){
        if(dm[i].lastChild){
        dm[i].appendChild(dmlist[i])
        }
      }
            for(let i=0;i<dm2.length;i++){
        if(dm2[i].lastChild){
        dm2[i].appendChild(dm2list[i])
        }
      }
      //         dm.forEach((v,index)=>{
      //   if(v.lastChild){
      //   v.appendChild(dmlist[index])
      //   }
      // })
      //               dm2.forEach((v,index)=>{
      //   if(v.lastChild){
      //   v.appendChild(dm2list[index])
      //   }
      // })
        document.querySelector("#out-table-1").appendChild(fix);
      } else {
        wb = XLSX.utils.table_to_book(document.querySelector("#out-table-1"));
      }
      let wbout = XLSX.write(wb, {
        bookType: "xlsx",
        bookSST: true,
        type: "array"
      });
      try {
        FileSaver.saveAs(
          new Blob([wbout], { type: "application/octet-stream" }),
          "抄表功能.xlsx"
        );
      } catch (e) {
        if (typeof console !== "undefined") console.log(e, wbout);
      }
      return wbout;
    },
    addStatistic(item){
      this.dialogForm_statistic = true;
      this.form_statistics.DS = item.DS
      this.form_statistics.LXBM = item.LXBM
      this.form_statistics.LXMC = item.LXMC
      this.form_statistics.SZQY = item.SZQY
      this.form_statistics.YLSJJWZ = item.YLSJJWZ
      this.form_statistics.ZDMC = item.ZDMC
    },
    appname_statistic(item,item2){
              this.dialogForm_statistic = false;
              if(item == 'ok_add'){
        let model = item2;
        let sbzt = model.SBZT == "未上报" ? 1 : 2;
        let json = JSON.stringify(model);
        this.$post(`api/PTGXSJCRK/InsertPtgxsjcrk?sbzt=${sbzt}`, json)
          .then(res => {
            if (res.data.Message == "OK") {
              this.open4();
              this.form_statistics = {...this.form_statistics_plac}
              this.$router.push({path:'/ordinaryStatisticsInformationManagement',query:{code:"ok_1"}})
            } else {
              this.open6();
            }
          });
              }

    },
    addList() {
      this.dialogForm = true;
      this.formtype = "add";
    },
    ok() {
      this.centerDialogVisible = false;
      this.deletList(this.id);
    },
    appname(item) {
      //表单提交
      if (item == "cancel") {
        this.dialogForm = false;
      }
      if (item == "ok_add" && this.formtype == "add") {
        //新增
        this.dialogForm = false;
        let model = this.form;
        let json = JSON.stringify(model);
        this.$post(`api/PTGXSJCRK/InsertPtgxsjcrkJc`, json).then(res => {
          if (res.data.Message == "OK") {
            this.open4();
            this.search();
            this.form = { ...this.form_plac };
          } else {
            this.open6();
          }
        });
      }
      if (item == "ok_add" && this.formtype == "edit") {
        //编辑
        this.dialogForm = false;
        let model = this.form;
        let json = JSON.stringify(model);
        this.$post(`api/PTGXSJCRK/UpdatePtgxsjcrkJc`, json).then(res => {
          if (res.data.Message == "OK") {
            this.open5();
            this.search();
            this.form = { ...this.form_plac };
          } else {
            this.open6();
          }
        });
      }
    },
    deletList(item) {
      this.$post(`api/PTGXSJCRK/DeletePtgxsjcrkJc/${item}`).then(res => {
        if (res.data.Message == "OK") {
          this.open3();
          this.search();
        } else {
          this.open6();
        }
      });
    },
    edit(item) {
      this.dialogForm = true;
      this.formtype = "edit";
      this.form = item;
    },
    open3() {
      this.$notify({
        title: "删除成功，数据已更新",
        type: "success"
      });
    },
    open4() {
      this.$notify({
        title: "添加成功，数据已更新",
        type: "success"
      });
    },
    open5() {
      this.$notify({
        title: "编辑成功，数据已更新",
        type: "success"
      });
    },
    open6() {
      this.$notify({
        title: "操作失败",
        type: "warning"
      });
    }
  },
  mounted() {
    this.$post(`api/PTGXSJCRK/GetPtgxsjcrkjcAllList?ds=&szqy=&sfqy=`)
      .then(res => {
        if (res.data.Message == "OK") {
          let data = JSON.parse(res.data.Data);
          this.loading = false;
          this.tableData3 = data;
        }
      });

    this.$post(`api/DW/GetDwList?ds=&szqy=`).then(res => {
      //
      if (res.data.Message == "OK") {
        let data = JSON.parse(res.data.Data);
        this.cityData = data;
        data.forEach(v => {
          if (v.JB == 2) {
            this.ds.push(v);
            this.formds.push(v);
          }
        });
        data.forEach(v => {
          if (v.JB == 3) {
            this.formszqy.push(v);
          }
        });
      }
    });    
  },
  watch: {
    select1(news) {
      this.szqy = [];
      this.select2 = "";
      this.cityData.forEach(v => {
        if (news == v.SJDWMC) {
          this.szqy.push(v);
        }
      });
    },
    dialogForm(news) {
      if (!news) {
        this.form = { ...this.form_plac };
      }
    }
  },
  computed: {}
};
</script>
<style >
</style>
