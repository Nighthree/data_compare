<template>
  <div class="home">
    <div class="container">
      <div>
        <h2 class="mb-0">上傳比對檔案</h2>
        <div class="dropZone mb-3">
          <input type="file" id="uploadFile" @change="uploadFile($event, 'uploadFile')" />
          <div class="dropBlock">
            <div><i class="fa fa-lg fa-cloud-upload"></i>拖曳檔案至此或是點擊上傳</div>
            <div v-if="!fileName">
              <small class="text-danger">
                限制
                <span v-for="(type, typeIndex) in type" :key="typeIndex"> .{{ type }}</span>
                檔案格式
              </small>
            </div>
            <div v-else class="text-center">
              <div>名稱：{{ fileName }}</div>
              <div><small>重新拖曳可覆蓋目前結果</small></div>
            </div>
          </div>
        </div>
        <div v-if="fileName" class="mb-3">
          <button type="button" class="btn btn-primary w-100" @click="startCompare">開始比對</button>
        </div>
      </div>

      <div v-if="hadCompare">
        <h2>比對結果</h2>
      </div>
    </div>

    <!-- 設定 -->
    <div class="setting" title="設定" data-bs-toggle="modal" data-bs-target="#exampleModal">
      <i class="fas fa-cog fa-2x"></i>
    </div>

    <!-- Modal -->
    <div class="modal fade" id="exampleModal" tabindex="-1" aria-labelledby="exampleModalLabel" aria-hidden="true">
      <div class="modal-dialog modal-xl modal-dialog-centered modal-dialog-scrollable">
        <div class="modal-content">
          <div class="modal-header">
            <h5 class="modal-title" id="exampleModalLabel">設定</h5>
            <button type="button" class="btn-close" data-bs-dismiss="modal" aria-label="Close"></button>
          </div>
          <div class="modal-body">...</div>
          <div class="modal-footer">
            <button type="button" class="btn btn-secondary" data-bs-dismiss="modal">關閉</button>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import ExcelJS from "exceljs";

export default {
  data() {
    return {
      fileName: null,
      type: ["xlsx"],
      column: [
        "A",
        "B",
        "C",
        "D",
        "E",
        "F",
        "G",
        "H",
        "I",
        "J",
        "K",
        "L",
        "M",
        "N",
        "O",
        "P",
        "Q",
        "S",
        "T",
        "U",
        "V",
        "W",
        "X",
        "Y",
        "Z",
      ],
      fileList: {
        beClass: {
          title: "BeClass報名表",
          header: [],
          headerIndex: 1,
          dataStart: 2,
          data: [],
          sheet: "比對報名表",
          mailIndex: "F",
          classList: [],
        },
        googleForm: {
          title: "Google 回應表單",
          header: [],
          headerIndex: 1,
          dataStart: 2,
          data: [],
          sheet: "比對表單",
          mailIndex: "C",
          final5CodeIndex: "E",
        },
        bank: {
          title: "銀行帳務(目前只適用郵局)",
          header: [],
          headerIndex: 1,
          dataStart: 2,
          data: [],
          sheet: "比對交易明細",
          final5CodeIndex: "E",
        },
      },
      hadCompare: false,
    };
  },
  methods: {
    // 上傳檔案
    uploadFile(event, domID) {
      const vm = this;
      const file = event.target.files[0];
      const fileName = file.name;
      const dom = document.getElementById(domID);
      const matchType = vm.judgeFileType(fileName);
      if (!matchType) {
        dom.value = "";
        return;
      }
      vm.fileName = fileName;
      vm.getFileContent(file, fileName);
    },
    // 檢查格式
    judgeFileType(fileName) {
      const vm = this;
      const name = fileName.split(".");
      const fileType = name[name.length - 1];
      const result = vm.type.some((item) => {
        return fileType.toLowerCase() == item.toLowerCase();
      });
      if (!result) {
        vm.$message({ type: "error", message: `檔案格式錯誤，請使用所指定的格式` });
      }
      return result;
    },
    // 取得檔案內容
    async getFileContent(file, fileName) {
      const vm = this;
      let fileType = fileName.split(".");
      fileType = fileType[fileType.length - 1].toLowerCase();
      const workbook = new ExcelJS.Workbook();
      const reader = new FileReader();

      if (fileType == "xlsx" || fileType == "xls") {
        reader.readAsArrayBuffer(file);
        reader.onload = () => {
          const buffer = reader.result;
          workbook.xlsx.load(buffer).then((wb) => {
            for (let attName in vm.fileList) {
              const direction = vm.fileList[attName];
              const sheetName = direction.sheet;
              const sheet = wb.getWorksheet(sheetName);
              const data = [];
              sheet.eachRow((row, rowNumber) => {
                const newRow = [...row.values];
                newRow.splice(0, 1);
                data.push(newRow);
              });
              vm.getDataByRules(data, direction);
            }
          });
        };
        return;
      }
      vm.$message({ type: "error", message: "錯誤，請檢查檔案條件" });
    },
    // 依範圍取得資料
    getDataByRules(data, direction) {
      const vm = this;
      const newData = [];
      data.forEach((item, index) => {
        const rowNumber = index + 1;
        if (rowNumber == direction.headerIndex) {
          direction.header = item;
          return;
        } else {
          if (rowNumber >= direction.dataStart) {
            newData.push({ hadExist: false, data: item });
          }
        }
      });
      direction.data = newData;
      console.log(direction.title);
      console.log(direction);
    },
    startCompare() {
      const vm = this;

      vm.hadCompare = true;
    },
  },
};
</script>
