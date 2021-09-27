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
        <div class="d-flex align-items-center mb-3">
          <h2 class="mb-0">比對結果</h2>
          <button
            type="button"
            class="btn btn-sm btn-outline-success ms-auto"
            data-bs-toggle="collapse"
            data-bs-target="#collapseExample"
            aria-expanded="false"
            aria-controls="collapseExample"
          >
            三份原始資料
          </button>
        </div>
        <div class="collapse" id="collapseExample">
          <div class="card card-body">
            <nav>
              <div class="nav nav-tabs" id="nav-tab" role="tablist">
                <button
                  class="nav-link active"
                  id="nav-home-tab"
                  data-bs-toggle="tab"
                  data-bs-target="#nav-home"
                  type="button"
                  role="tab"
                  aria-controls="nav-home"
                  aria-selected="true"
                >
                  {{ fileList.beClass.title }}
                </button>
                <button
                  class="nav-link"
                  id="nav-profile-tab"
                  data-bs-toggle="tab"
                  data-bs-target="#nav-profile"
                  type="button"
                  role="tab"
                  aria-controls="nav-profile"
                  aria-selected="false"
                >
                  {{ fileList.googleForm.title }}
                </button>
                <button
                  class="nav-link"
                  id="nav-contact-tab"
                  data-bs-toggle="tab"
                  data-bs-target="#nav-contact"
                  type="button"
                  role="tab"
                  aria-controls="nav-contact"
                  aria-selected="false"
                >
                  {{ fileList.bank.title }}
                </button>
              </div>
            </nav>
            <div class="tab-content" id="nav-tabContent">
              <div class="tab-pane fade show active" id="nav-home" role="tabpanel" aria-labelledby="nav-home-tab">
                <div class="table-responsive">
                  <table class="table table-striped table-sm table-hover">
                    <thead>
                      <tr>
                        <th v-for="th in fileList.beClass.header" :key="th">{{ th }}</th>
                        <th>狀態</th>
                      </tr>
                    </thead>
                    <tbody>
                      <tr v-for="(item, index) in fileList.beClass.data" :key="index">
                        <td v-for="(td, tdIndex) in item.data" :key="tdIndex">{{ td }}</td>
                        <td>{{ item.state }}</td>
                      </tr>
                    </tbody>
                  </table>
                </div>
              </div>
              <div class="tab-pane fade" id="nav-profile" role="tabpanel" aria-labelledby="nav-profile-tab">
                <div class="table-responsive">
                  <table class="table table-striped table-sm table-hover">
                    <thead>
                      <tr>
                        <th v-for="th in fileList.googleForm.header" :key="th">{{ th }}</th>
                        <th>狀態</th>
                      </tr>
                    </thead>
                    <tbody>
                      <tr v-for="(item, index) in fileList.googleForm.data" :key="index">
                        <td v-for="(td, tdIndex) in item.data" :key="tdIndex">{{ td }}</td>
                        <td>{{ item.state }}</td>
                      </tr>
                    </tbody>
                  </table>
                </div>
              </div>
              <div class="tab-pane fade" id="nav-contact" role="tabpanel" aria-labelledby="nav-contact-tab">
                <div class="table-responsive">
                  <table class="table table-striped table-sm table-hover">
                    <thead>
                      <tr>
                        <th v-for="th in fileList.bank.header" :key="th">{{ th }}</th>
                        <th>狀態</th>
                      </tr>
                    </thead>
                    <tbody>
                      <tr v-for="(item, index) in fileList.bank.data" :key="index">
                        <td v-for="(td, tdIndex) in item.data" :key="tdIndex">{{ td }}</td>
                        <td>{{ item.state }}</td>
                      </tr>
                    </tbody>
                  </table>
                </div>
              </div>
            </div>
          </div>
        </div>

        <div class="table-responsive">
          <table class="table table-striped table-sm table-hover">
            <thead>
              <tr>
                <th>姓名</th>
                <th>email</th>
                <th>課程內容</th>
                <th>應附金額</th>
                <th>實附金額</th>
                <th>狀態</th>
              </tr>
            </thead>
            <tbody>
              <tr></tr>
            </tbody>
          </table>
        </div>
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
          <div class="modal-header bg-dark text-white fw-bold">
            <h5 class="modal-title" id="exampleModalLabel">設定比對參數</h5>
            <button type="button" class="btn-close" data-bs-dismiss="modal" aria-label="Close"></button>
          </div>
          <div class="modal-body">
            <!-- BeClass報名表 -->
            <div class="card mb-3">
              <div class="card-header bg-primary text-white fw-bold">{{ fileList.beClass.title }}</div>
              <div class="card-body">
                <form class="row g-3">
                  <div class="col-12">
                    <label for="beClassForm" class="form-label"><small>表單比對名稱</small></label>
                    <input type="text" class="form-control form-control-sm" id="beClassForm" v-model="fileList.beClass.sheet" />
                  </div>
                  <div class="col-6">
                    <label for="beClassHeader" class="form-label"><small>表頭位置</small></label>
                    <input type="number" class="form-control form-control-sm" id="beClassHeader" v-model.number="fileList.beClass.headerIndex" />
                  </div>
                  <div class="col-6">
                    <label for="beClassData" class="form-label"><small>資料起始位置</small></label>
                    <input type="number" class="form-control form-control-sm" id="beClassData" v-model.number="fileList.beClass.dataStart" />
                  </div>

                  <div class="col-12">
                    <div class="d-flex align-items-center mb-3">
                      <div>課程參數</div>
                      <!-- <div class="ms-3">
                        <select class="form-select form-select-sm" id="">
                          <option selected disabled>模板:開發中</option>
                        </select>
                      </div> -->
                    </div>
                    <div class="row flex-wrap">
                      <div class="col-4 mb-3" v-for="(item, index) in fileList.beClass.classList" :key="index">
                        <div class="d-flex justify-content-between">
                          <label class="form-label"><small>課程種類名稱</small></label>
                          <button type="button" class="btn btn-sm btn-outline-danger" @click="deleteClass(index)"><i class="fas fa-trash-alt"></i></button>
                        </div>
                        <input type="text" class="form-control form-control-sm" placeholder="請輸入種類名稱" v-model="item.name" />
                        <label class="form-label"><small>課程種類欄位</small></label>
                        <select class="form-select form-select-sm" v-model="item.index">
                          <option :value="col" v-for="col in column" :key="col">{{ col }}</option>
                        </select>
                        <label class="form-label"><small>課程數量</small></label>
                        <select class="form-select form-select-sm" v-model.number="item.content">
                          <option :value="num" v-for="num in 10" :key="num">{{ num }}</option>
                        </select>
                      </div>
                      <div class="col-4 d-flex align-items-end mb-3">
                        <button type="button" class="btn btn-sm btn-outline-primary" @click="addClass">新增種類</button>
                      </div>
                    </div>
                  </div>
                  <div>
                    <small class="text-secondary"> 與 {{ fileList.googleForm.title }} 比對欄位</small>
                  </div>
                  <div class="col-6">
                    <label for="beClassMail" class="form-label"><small>mail 欄位</small></label>
                    <select class="form-select form-select-sm" id="beClassMail" v-model="fileList.beClass.mailIndex">
                      <option :value="col" v-for="col in column" :key="col">{{ col }}</option>
                    </select>
                  </div>
                </form>
              </div>
            </div>
            <!-- google 回應表單 -->
            <div class="card mb-3">
              <div class="card-header bg-primary text-white fw-bold">{{ fileList.googleForm.title }}</div>
              <div class="card-body">
                <form class="row g-3">
                  <div class="col-12">
                    <label for="googleFormForm" class="form-label">表單比對名稱</label>
                    <input type="text" class="form-control form-control-sm" id="googleFormForm" v-model="fileList.googleForm.sheet" />
                  </div>
                  <div class="col-6">
                    <label for="googleFormHeader" class="form-label">表頭位置</label>
                    <input type="number" class="form-control form-control-sm" id="googleFormHeader" v-model.number="fileList.googleForm.headerIndex" />
                  </div>
                  <div class="col-6">
                    <label for="googleFormData" class="form-label">資料起始位置</label>
                    <input type="number" class="form-control form-control-sm" id="googleFormData" v-model.number="fileList.googleForm.dataStart" />
                  </div>
                  <div>
                    <small class="text-secondary"> 與 {{ fileList.beClass.title }} 比對欄位</small>
                  </div>
                  <div class="col-6">
                    <label for="googleFormMail" class="form-label">mail 欄位</label>
                    <select class="form-select form-select-sm" id="beClassMail" v-model="fileList.googleForm.mailIndex">
                      <option :value="col" v-for="col in column" :key="col">{{ col }}</option>
                    </select>
                  </div>
                  <div>
                    <small class="text-secondary"> 與 {{ fileList.bank.title }} 比對欄位</small>
                  </div>
                  <div class="col-6">
                    <label for="googleFormFinal5CodeIndex" class="form-label">關鍵字欄位 (末五碼)</label>
                    <select class="form-select form-select-sm" id="googleFormFinal5CodeIndex" v-model="fileList.googleForm.final5CodeIndex">
                      <option :value="col" v-for="col in column" :key="col">{{ col }}</option>
                    </select>
                  </div>
                  <div class="col-6">
                    <label for="googleFormFinal5CodeIndex" class="form-label">匯款日期欄位</label>
                    <select class="form-select form-select-sm" id="googleFormFinal5CodeIndex" v-model="fileList.googleForm.dateIndex">
                      <option :value="col" v-for="col in column" :key="col">{{ col }}</option>
                    </select>
                  </div>
                  <div class="col-6">
                    <label for="googleFormFinal5CodeIndex" class="form-label">匯款時間欄位</label>
                    <select class="form-select form-select-sm" id="googleFormFinal5CodeIndex" v-model="fileList.googleForm.timeIndex">
                      <option :value="col" v-for="col in column" :key="col">{{ col }}</option>
                    </select>
                  </div>
                </form>
              </div>
            </div>

            <!-- 銀行 -->
            <div class="card mb-3">
              <div class="card-header bg-primary text-white fw-bold">{{ fileList.bank.title }}</div>
              <div class="card-body">
                <form class="row g-3">
                  <div class="col-12">
                    <label for="bankForm" class="form-label">表單比對名稱</label>
                    <input type="text" class="form-control form-control-sm" id="bankForm" v-model="fileList.bank.sheet" />
                  </div>
                  <div class="col-6">
                    <label for="bankHeader" class="form-label">表頭位置</label>
                    <input type="number" class="form-control form-control-sm" id="bankHeader" v-model.number="fileList.bank.headerIndex" />
                  </div>
                  <div class="col-6">
                    <label for="bankData" class="form-label">資料起始位置</label>
                    <input type="number" class="form-control form-control-sm" id="bankData" v-model.number="fileList.bank.dataStart" />
                  </div>
                  <div>
                    <small class="text-secondary"> 與 {{ fileList.googleForm.title }} 比對欄位</small>
                  </div>
                  <div class="col-6">
                    <label for="bankFinal5CodeIndex" class="form-label">關鍵字欄位 (末五碼)</label>
                    <select class="form-select form-select-sm" id="bankFinal5CodeIndex" v-model="fileList.bank.final5CodeIndex">
                      <option :value="col" v-for="col in column" :key="col">{{ col }}</option>
                    </select>
                  </div>
                  <div class="col-6">
                    <label for="bankFinal5CodeIndex" class="form-label">匯款日期、時間欄位</label>
                    <select class="form-select form-select-sm" id="bankFinal5CodeIndex" v-model="fileList.bank.dateIndex">
                      <option :value="col" v-for="col in column" :key="col">{{ col }}</option>
                    </select>
                  </div>

                  <div class="col-6">
                    <label for="bankPermitTime" class="form-label">允許匯款時間 +- 誤差 ( 單位: 分鐘 )</label>
                    <input type="number" class="form-control form-control-sm" id="bankPermitTime" v-model.number="fileList.bank.permitTime" />
                  </div>
                  <!-- <div class="col-6">
                    <label for="bankFinal5CodeIndex" class="form-label">匯款時間欄位</label>
                    <select class="form-select form-select-sm" id="bankFinal5CodeIndex" v-model="fileList.bank.timeIndex">
                      <option :value="col" v-for="col in column" :key="col">{{ col }}</option>
                    </select>
                  </div> -->
                </form>
              </div>
            </div>
          </div>
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
      column: ["A", "B", "C", "D", "E", "F", "G", "H", "I", "J", "K", "L", "M", "N", "O", "P", "Q", "S", "T", "U", "V", "W", "X", "Y", "Z"],
      fileList: {
        beClass: {
          title: "BeClass報名表",
          header: [],
          headerIndex: 1,
          dataStart: 2,
          data: [],
          sheet: "比對報名表",
          mailIndex: "F",
          classList: [
            { name: "晚上冥想班", index: "G", content: 2 },
            { name: "早上冥想班", index: "J", content: 2 },
          ],
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
          dateIndex: "F",
          timeIndex: "G",
        },
        bank: {
          title: "銀行帳務(目前只適用郵局)",
          header: [],
          headerIndex: 1,
          dataStart: 2,
          data: [],
          sheet: "比對交易明細",
          final5CodeIndex: "G",
          dateIndex: "A",
          permitTime: 30, // 單位分中
        },
      },
      hadCompare: true,
      totalData: [],
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
        // const modal = new bootstrap.Modal(document.getElementById("exampleModal"));
        // modal.show();
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
            const headerLen = direction.header.length;
            const itemLen = item.length;
            console.log("headerLen", headerLen, "itemLen", itemLen);
            for (let i = 0; i < headerLen - itemLen; i++) {
              item.push("");
            }
            newData.push({ state: 0, data: item });
          }
        }
      });
      direction.data = newData;
      console.log(direction.title);
      console.log(direction);
    },
    // 取得位置
    getColumnIndex(text) {
      const vm = this;
      return vm.column.indexOf(text);
    },
    // 刪除課程
    deleteClass(index) {
      const vm = this;
      vm.fileList.beClass.classList.splice(index, 1);
    },
    // 新增課程
    addClass() {
      const vm = this;
      const obj = { name: "", index: null, content: 1 };
      vm.fileList.beClass.classList.push(obj);
    },
    // 開始比對
    startCompare() {
      const vm = this;
      vm.hadCompare = true;
      const beClassData = vm.fileList.beClass.data;
      const googleData = vm.fileList.googleForm.data;
      const bankData = vm.fileList.bank.data;

      //取得各種變數
      const beMI = vm.column.indexOf(vm.fileList.beClass.mailIndex);
      const gMI = vm.column.indexOf(vm.fileList.googleForm.mailIndex);
      const g5I = vm.column.indexOf(vm.fileList.googleForm.final5CodeIndex);
      const gDI = vm.column.indexOf(vm.fileList.googleForm.dateIndex);
      const gTI = vm.column.indexOf(vm.fileList.googleForm.timeIndex);
      const b5I = vm.column.indexOf(vm.fileList.bank.final5CodeIndex);
      const bDI = vm.column.indexOf(vm.fileList.bank.dateIndex);

      const data = [];

      beClassData.forEach((beClass) => {
        beClass.google = [];
      });

      beClassData.forEach((beClass) => {
        googleData.forEach((google) => {
          //比對 mail
          if (!!beClass.data[beMI] && !!google.data[gMI]) {
            if (beClass.data[beMI].toLowerCase() === google.data[gMI].toLowerCase()) {
              google.state++;
              beClass.google.push(google);
            }
          }
        });
        data.push(beClass);
      });

      googleData.forEach((google) => {
        google.bank = [];
        let g5 = `${google.data[g5I]}`;
        if (!!g5 && g5.length >= 5) {
          g5 = g5.substr(g5.length - 5, 5);
        }
        // 取得表單回報的匯款時間
        const googleDate = google.data[gDI];
        const googleTime = google.data[gTI];
        const newGDate = new Date(googleDate.getFullYear(), googleDate.getMonth(), googleDate.getDate(), googleTime.getHours(), googleTime.getMinutes(), 0).getTime();
        // 允許誤差時間範圍
        const permitTime = vm.fileList.bank.permitTime * 60 * 1000;
        const start = newGDate - permitTime;

        const end = newGDate + permitTime;
        // console.log("start", start, "end", end);
        // 比對末五碼
        bankData.forEach((bank) => {
          let bank5 = `${bank.data[b5I]}`;
          if (!!bank5 && bank5.length >= 5 && !!g5 && g5.length >= 5) {
            bank5 = bank5.substr(bank5.length - 5, 5);
            // console.log(`${g5}`, `${bank5}`, `${g5}` === `${bank5}`);
            if (`${g5}` === `${bank5}`) {
              // 取得銀行交易時間
              const bankLogTime = bank.data[bDI].split(" ");
              const logDate = bankLogTime[0].split("/");
              const logTime = bankLogTime[1].split(":");
              const bankDate = new Date(
                1911 + Number(logDate[0]),
                Number(logDate[1] - 1),
                Number(logDate[2]),
                Number(logTime[0]),
                Number(logTime[1]),
                Number(logTime[2])
              ).getTime();
              console.log("銀行匯入時間", bankDate);
              console.log(bankDate >= start, end >= bankDate, bankDate >= start && end >= bankDate);
              if (bankDate >= start && end >= bankDate) {
                console.log("推送資料");
                bank.state++;
                google.bank.push(bank);
              }
            }
          }
        });
      });

      console.log(data);
    },
  },
  mounted() {
    const vm = this;
  },
};
</script>
