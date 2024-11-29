---
date: 2024-11-19
type: fact
aliases:
  -
hubs:
  - "[[]]"
---

# mirle dashboard export button issue check

💬:
```vue
~/D/g/mirle-dashboard-frontend > cat src/components/snippets/Ml_button.vue 
<script setup lang="ts">
import { ref, computed, watch } from "vue";
import type { SnippetBasicProps, SnippetDynamicParmProps } from "@/types/types";
import createBaseMixin from "@/helpers/base";
import { dynamicParmsStore } from "@/stores/dynamicParms";
import { usePageTreeStore } from "@/stores/pageTree";
import { useSnippetDefaultSetting } from "@/hooks/snippet/useSnippetDefaultSetting";
import { useDynamicParameter } from "@/hooks/snippet/useDynamicParameter";
useDynamicParameter
const { defaultSnippetStyle } = useSnippetDefaultSetting();
const { updateFieldNameTemporaryParms } = useDynamicParameter();
const props = defineProps<SnippetBasicProps & SnippetDynamicParmProps & {
  type?: string;
  event?: string;
  buttonLabel?: string;
  buttonSize?: string;
  buttonColor?: string;
  queryEvent?: {
    groupName?: [];
  };
  multipleSelect?: boolean;
}>();

const {
  getSnippetData,
  exportSnippetData,
  getValueWithUnit
} = createBaseMixin(props);

// init snippetData
getSnippetData(null);

const pageTreeStore = usePageTreeStore();
const parmsStore = dynamicParmsStore();
const defaultStyle = defaultSnippetStyle('snippet_button');

const type = computed(() => { return props.type });
const tabToggle = ref();
const loading = ref(false)
/** Style */
const buttonLabel = computed(() => { return props.buttonLabel ?? defaultStyle.label });
const buttonColor = computed(() => { return props.buttonColor ?? defaultStyle.color });
const buttonSize = computed(() => { return props.buttonSize ?? defaultStyle.size });

/** Tab */
const templateOptions = computed(() => {
  let number: number = props.optionsNumber ?? 3;
  const loopList = Array.from({ length: number }).map((_, index) => ({
    label: props.optionsSetting?.[index]?.label ?? `Label${index + 1}`,
    value: props.optionsSetting?.[index]?.value ?? `value${index + 1}`,
  }));

  return loopList;
});

/**
 * 更新UI值
 * @param propsDict     屬性物件
 */
const updateTabUIValue = (propsDict: { [key: string]: any },) => {
  if (propsDict.fieldValue.fieldName && propsDict.optionsSetting) {
    let fieldValue = propsDict.fieldValue.fieldName;
    let defaultValue = fieldValue;

    if (propsDict.multipleSelect) {
      tabToggle.value = propsDict.optionsSetting.filter((item: any) => defaultValue.includes(item.value)).map((item: any) => item.label);
    } else {
      if (fieldValue.includes(',')) { defaultValue = fieldValue?.split(','); }
      Object.keys(propsDict.optionsSetting).forEach((item: any) => {
        if (JSON.stringify(propsDict.optionsSetting[item].value) === JSON.stringify(defaultValue)) {
          tabToggle.value = propsDict.optionsSetting[item].label
        }
      })
    }
  }
};

if (props.type === 'tab') {
  updateTabUIValue(props)
  const updatePayload = {
    propsDict: props,
    updateValue: tabToggle.value
  };
  updateFieldNameTemporaryParms('button', updatePayload)
}

/**
 * 非編輯模式點擊觸發事件
 */
const clickEvent = () => {
  if (!pageTreeStore.isEditMode) {
    const groupNameList = props.queryEvent?.groupName;

    if (props.type === "tab" && props.fieldName?.fieldName) {
      const updatePayload = {
        updateValue: tabToggle.value,
        propsDict: props
      };
      updateFieldNameTemporaryParms('button', updatePayload);
      parmsStore.mlEvent.search('search', groupNameList, [], props);
    } else {
      switch (props.event) {
        case "search":
          parmsStore.mlEvent.search('search', groupNameList, [], props);
          break;
        case "export":
          const baseURL = import.meta.env.VITE_BASE_URL ?? "";
          parmsStore.mlEvent.export('export', groupNameList, [], props);
          exportSnippetData();
          break;
        default:
          // 提醒選擇事件否則按下無效果
          console.error("Please choose a event for Button Snippet");
          break;
      }
    }
  }
};

/** 監控頁面元件更新*/
watch(props, (newValue) => {
  if (type.value === "tab" && newValue.fieldName?.fieldName) {
    updateTabUIValue(newValue)
    const updatePayload = {
      propsDict: props,
      updateValue: tabToggle.value,
    };
    updateFieldNameTemporaryParms('button', updatePayload)
  }
}, { deep: true });
</script>

<template>
  <div class="button-layout" :style="{ width: getValueWithUnit(props.width, 'button', 'width') }">
    <v-btn-toggle v-if="type === 'tab'" mandatory :multiple="props.multipleSelect" :color="buttonColor"
      v-model="tabToggle" :disabled="loading">
      <template v-for="element in templateOptions" :key="element">
        <v-btn class="ma-1 btn-style" :size="buttonSize" :value="element.label" @click="clickEvent">
          {{ element.label }}
        </v-btn>
      </template>
    </v-btn-toggle>
    <v-btn v-else v-bind="props" class="text-none " :block="true" :size="buttonSize" :color="buttonColor"
      @click="clickEvent" :disabled="loading">
      {{ buttonLabel }}
    </v-btn>
  </div>
</template>

<style lang="scss" scoped>
.button-layout {
  height: 100%;
  width: 100%;
  display: flex;
  align-items: center;
}

.btn-style {
  border: 1px solid #FFF !important;
  border-radius: 7px !important;
}
</style>
```

```ts
~/D/g/mirle-dashboard-frontend > cat src/stores/dynamicParms.ts
import { defineStore } from "pinia";
import { reactive, inject, ref } from "vue";
import { useRouter } from 'vue-router';
import type { AxiosStatic } from 'axios';
import type { Emitter } from "mitt"
import type { Events } from "@/types/events"
import { usePageTreeStore } from '@/stores/pageTree'
import { useLayout, useTabLayout } from "@/hooks/useLayout";
import { useDynamicParameter } from "@/hooks/snippet/useDynamicParameter";
import { useSnippetDefaultData } from "@/hooks/snippet/useSnippetDefaultData";

interface groupData {
  dataGroupName?: string;                         // 群組名稱
  dataUpdatePath?: Array<string> | null;          // 該群組名稱的元件路徑
}

/**
 * 參數輸入型態:
 * Input類型(參數下拉選單選項固定為使用者輸入的FieldName)："text" | "number" | "date" | "daterange" | "radio" | "checkbox" | "select" 
 * 補充資料參數(參數下拉選單選項固定為 #KEYNAME)："custominformation"
 * 連動參數(參數下拉選單選項固定為 @KEYNAME)："linkedDataParms" 
 */
interface fieleParms {
  inputType: string;                              // 參數輸入型態
  inputValue: string | Array<string> | null;      // 參數輸入值
  isMultiple: boolean;                            // (Input類型)是否為多選值
  isUpdateValue: boolean;                         // (Input類型)是否為更新值
  isInformation: boolean;                         // 是否為補充資料的查詢參數
}

interface resultParmsProps {
  isInit: boolean;                                // 是否為初始查詢
  isTabInit: boolean;                             // 是否為初始查詢(TabLayout)
  searchGroup: Array<string>;                     // Button元件群組名稱
  dataUpdatePath: Array<string>;                  // 查詢資料元件路徑
  parmsUpdatePath: Array<string>;                 // 查詢參數元件路徑
  allDynaimcParms: { [key: string]: any; };       // 查詢參數內容
}

interface exportParms {
  key: string;                                    // 匯出datasource key
  isDynamicData: boolean;                         // 該datasource 是否是動態資料
  dataGroupName: string;                          // 該頁面匯出的相關資料群組名稱
  dashboardType: string;                          // 此datasource是產生哪種圖表類型
  params: { [key: string]: any; };                // 後端搜尋參數集
  queryParamsMap: { [key: string]: any; };        // 搜尋參數與input filename對應資料集
  exportItem: { [key: string]: any; };            // 紀錄popo資料
}

interface exportFileItems {
  isHaveGroup: boolean;                           // 該匯出頁面是否有群集設定
  exportParmsList: Array<exportParms>;            // 要送往後端查詢需要使用的list
  sourceKeys: Array<string>                       // key list
}

export const dynamicParmsStore = defineStore("dynamicParms", () => {
  const {
    snippetsDataMap,
    snippetsDataFormat,
    selectMultilayerDefaultValue,
    selectSinglelayerDefaultValue,
    processDate
  } = useSnippetDefaultData();
  const { getUrlParams, processSelectData, getPageSreachParms } = useDynamicParameter();
  const router = useRouter();
  const axios = inject('axios') as AxiosStatic;
  const emitter = inject("emitter") as Emitter<Events>
  const pageTreeStore = usePageTreeStore();

  const groupDataPath = reactive<groupData[]>([]);
  const temporaryParms = reactive<{ list: { [key: string]: fieleParms } }>({
    list: {}
  });

  const resultParms = reactive<resultParmsProps>({
    isInit: true,
    isTabInit: false,
    searchGroup: [],
    dataUpdatePath: [],
    parmsUpdatePath: [],
    allDynaimcParms: {},
  });
  const exportFileParms = reactive<exportFileItems>({
    isHaveGroup: false,
    exportParmsList: [],
    sourceKeys: []
  });

  // 檢查相關UI輸入值
  const dynamicParmNameChecker = reactive<any>({
    triggerName: {},
    fieldName: []
  });

  // 設定參數符號
  const triggerParmsSymbol = '@';
  const infoParmsSymbol = '#';

  /**
   * 取得當前頁面元件路徑
   * @param resultObject      resultParms
   * @param pageTree          當前頁面樹狀結構(包含TabLaayout所有Children)
   */
  const getPageSnippetsPath = (resultObject: any, pageTree: TreeNode) => {
    let getAllChildren = useLayout(pageTree).getChildren();

    getAllChildren?.forEach((pageItem: TreeNode) => {
      switch (pageItem.snippetType) {
        case 'layout':
          getPageSnippetsPath(resultObject, pageItem)
          break;

        case 'tabLayout':
          let ActiveChildren = useTabLayout(pageItem).getActiveChildren();
          ActiveChildren?.forEach(() => { getPageSnippetsPath(resultObject, pageItem) })
          break;

        default:
          let getDataGroup = !!(
            pageItem.snippetProps.dataSourceNumber > 0 &&
            (pageItem.snippetType !== 'input' || (pageItem.snippetType === 'input' && pageItem.snippetProps.type === 'select'))
          );
          let getParmsGroup = !!(
            pageItem.snippetProps.dataSourceNumber === 0 &&
            pageItem.snippetProps.hyperLink
          );
          let snippetPath = pageItem.path;
          let dataSourceGroupName = pageItem.snippetProps.dataSourceGroupName;


          // 需更新資料的元件路徑
          if (getDataGroup) {
            if (dataSourceGroupName && !exportFileParms.isHaveGroup) {
              exportFileParms.isHaveGroup = true;
            }
            if (snippetPath && !dataSourceGroupName && !resultObject.dataUpdatePath?.includes(snippetPath)) {
              resultObject.dataUpdatePath.push(snippetPath);
            }
            exportProcessParms(pageItem);
          }

          // 需更新參數的元件路徑
          if (getParmsGroup && snippetPath && !dataSourceGroupName && !resultObject.parmsUpdatePath.includes(snippetPath)) {
            resultObject.parmsUpdatePath.push(snippetPath);
          }
          break;
      }
    })
  };

  /**
   * 處理選單參數值(Input Select)
   * @param mode              pageInit(初始頁面)|dataSourceClick(編輯模式資料查詢)|inputInit(元件初始)|editorWatch(Input編輯模式)|modelWatch(Input檢視模式)
   * @param propsDict         元件屬性物件
   * @param updateData        更新值(URLParms or fieldValue)
   * @param URLParms          URL參數
   * @param typeDefaultData   不同型態的的資料(for editorWatch)
   * @returns 
   */
  const processSelectParmsValue = async (mode: string, propsDict: { [key: string]: any }, updateData: any, URLParms: any, typeDefaultData: any) => {
    let condition = false;
    let selectDefaultDict: { [key: string]: any } = {};
    let DataList = snippetsDataMap.inputSelect.defaultValue;        // 選單資料
    let LevelNumber = 0;                                            // 選單層數
    let isMultiple = propsDict.multipleSelect;                      // 是否為多選值
    let isMultiLayer = propsDict.dataSourceSetting[0].isMultiLayer; // 是否為多層選單
    let useURLParm = URLParms ? true : false;                       // 是否使用URL參數
    let fieldName = propsDict.fieldName?.fieldName;                 // 使用者輸入名稱

    // 根據不同模式進行處理
    // 1. DataList:     判斷是否有資料來源
    // 2. isMultiLayer: 判斷是否為多層選單
    // 3. condition:    判斷是否進行預設值處理
    if (mode === 'pageInit' || mode === 'dataSourceClick') {
      if (propsDict.dataSourceSetting[0].dataSource) {
        let matchData = true;
        try {
          // 靜態資料來源
          let payload = { key: propsDict.dataSourceSetting[0].dataSource, params: {} };
          let defaultDataList = (await axios.put("/dynamic_data/get_dynamic_data", payload)).data;
          const dataVerification = snippetsDataFormat.inputSelect(defaultDataList);
          if (dataVerification.match) {
            DataList = dataVerification.data;
          } else {
            matchData = false;
          }
        } catch (error) {
          console.error(`Error dataSource:${propsDict.dataSourceSetting[0].dataSource}\n`, error);
        }

        // 確認是否符合條件，取得選單資料
        if (matchData) {
          let defaultLabels = [...new Set(DataList.map((item: any) => item.level_label))];
          LevelNumber = defaultLabels.length;
          isMultiLayer = LevelNumber > 1 ? true : false;
          condition = DataList.length > 0 ? true : false;
        }
      }
    } else if (mode === 'inputInit') {
      // 等待事件觸發取得初始選單內容(儲存時已經是符合條件的資料來源)
      let defaultSelectList: any = await initListEvent(fieldName);
      DataList = processSelectData(defaultSelectList.defaultDict, propsDict);
      let defaultLabels = [...new Set(DataList.map((item: any) => item.level_label))];
      LevelNumber = defaultLabels.length;
      isMultiLayer = defaultLabels.length > 1 ? true : false;
      condition = true;
    } else if (mode === 'editorWatch') {
      DataList = typeDefaultData?.rawSelectData;
      LevelNumber = typeDefaultData?.labelSelectListNumber;
      isMultiLayer = LevelNumber > 1 ? true : false;

      if (updateData) {
        let valueList = (updateData as string).split('.');
        let haveValue = valueList?.every((item: any) => item != '');
        condition = valueList.length === LevelNumber && haveValue ? true : false;
      }
    }

    propsDict.dataSourceSetting[0].isMultiLayer = isMultiLayer;
    // 預設值處理
    if (condition && isMultiLayer) {
      // 多層選單
      selectDefaultDict = selectMultilayerDefaultValue(updateData, DataList, LevelNumber, isMultiple, useURLParm);
    } else if (condition) {
      // 單層選單
      selectDefaultDict = selectSinglelayerDefaultValue(updateData, DataList, isMultiple, useURLParm);
    }

    return {
      'typeDefaultData': typeDefaultData,
      'selectDefaultDict': selectDefaultDict,
      'DataList': DataList,
      'LevelNumber': LevelNumber,
      'isMultiLayer': isMultiLayer
    }
  };

  const exportProcessParms = (element: any) => {
    // 僅針對 pie barline datagrid匯出
    if ((element.snippetType === 'pie' || element.snippetType === 'barline' || element.snippetType === 'datagrid' || element.snippetType === 'datagridServer')) {
      element.snippetProps.dataSourceSetting?.forEach((dataSourceItem: any) => {
        let itemMap = reactive<exportParms>({
          key: '',
          isDynamicData: false,
          dashboardType: '',
          dataGroupName: '',
          params: {},
          queryParamsMap: {},
          exportItem: {},
        });
        // 排除重複但是如果是群組則需要也被匯出
        if (!exportFileParms.sourceKeys.includes(dataSourceItem.dataSource) || (exportFileParms.isHaveGroup)) {
          itemMap.key = dataSourceItem.dataSource;
          itemMap.dashboardType = element.snippetType;
          itemMap.isDynamicData = dataSourceItem.isDynamicData;
          itemMap.queryParamsMap = dataSourceItem.queryParameter;
          itemMap.exportItem = element.path;
          exportFileParms.exportParmsList.push(itemMap);
          exportFileParms.sourceKeys.push(dataSourceItem.dataSource)
        }
      })
    }
  };

  /**
   * 更新最終查詢參數
   * @param mode                  search|export|dataSourceClick 
   * @param groupNameList         ML_Button元件的群組名稱
   * @param pathList              元件路徑(path)
   * @param propsDict             元件屬性物件(props)
   */
  const commonProcessParms = async (mode: string, groupNameList: any, pathList: Array<any>, propsDict: { [key: string]: any }) => {
    // 取得ML_Button元件的群組名稱
    if ((propsDict.type === 'basic' || propsDict.type === 'tab') && groupNameList) {
      resultParms.searchGroup = groupNameList;
    }

    // 取得更新資料的元件路徑
    resultParms.isInit = false;
    resultParms.dataUpdatePath = [];
    resultParms.parmsUpdatePath = [];

    // tirrger snippest 有設定群組，使用的元件也要設定群組
    if (mode === 'dataSourceClick') {
      // 編輯模式下，點擊按鈕只更新該資料來源的元件路徑
      resultParms.dataUpdatePath = pathList;
    } else {
      // 查詢模式下，點擊按鈕更新元件路徑
      if (groupNameList && groupNameList.length > 0) {
        // Button有指定群組時，取得查詢頁面上有該群組名稱的元件路徑
        groupDataPath?.forEach((item: any) => {
          if (groupNameList.includes(item.dataGroupName)) {
            item.dataUpdatePath.forEach((path: string) => {
              if (!resultParms.dataUpdatePath.includes(path)) {
                resultParms.dataUpdatePath.push(path)
              }
            })
            item.parmsUpdatePath.forEach((path: string) => {
              if (!resultParms.parmsUpdatePath.includes(path)) {
                resultParms.parmsUpdatePath.push(path)
              }
            })
          }
        })
      } else if (pageTreeStore.tree) {
        // 初始時，取得查詢頁面上所有元件路徑
        // Button沒有指定群組時，取得查詢頁面上沒有指定群組的元件路徑
        getPageSnippetsPath(resultParms, pageTreeStore.tree)
      }
    }

    // 取得查詢參數
    if (temporaryParms.list) {
      await Promise.all(Object.keys(temporaryParms.list).map(async (fieldName: any) => {
        const TemporaryDict = temporaryParms.list[fieldName];
        // 編輯模式下更新該參數值
        const dataSourceClickSelect = !!(
          pageTreeStore.tree &&
          mode === 'dataSourceClick' &&
          TemporaryDict.inputType === 'select'
        );

        if (dataSourceClickSelect) {
          // 取得頁面查詢參數屬性(props)
          const parmDict: { [key: string]: any; } = {};
          getPageSreachParms(parmDict, (pageTreeStore.tree as TreeNode), false)
          const itemProps = parmDict[fieldName];
          // 處理參數預設值
          const URLParmDict = getUrlParams(router);
          const URLParms = URLParmDict?.[fieldName];
          const updateData = URLParms ?? itemProps.fieldValue?.fieldName
          const selectParmsDict: any = await processSelectParmsValue(mode, itemProps, updateData, URLParms, null)
          const selectDefaultDict = selectParmsDict.selectDefaultDict;
          resultParms.allDynaimcParms[fieldName] = selectDefaultDict?.value;
        } else {
          resultParms.allDynaimcParms[fieldName] = TemporaryDict.inputValue;
        }
      }));
    }

    // 更新查詢參數
    emitter.emit("updateResultParms", resultParms)
  }

  /**
   * 按鈕事件
   */
  const dynamic_search_event = ref(false)
  const mlEvent = {
    search: (mode: string, groupNameList: any, pathList: Array<any>, propsDict: { [key: string]: any }) => {
      console.log('動態查詢');
      dynamic_search_event.value = true
      commonProcessParms(mode, groupNameList, pathList, propsDict)
    },
    export: (mode: string, groupNameList: any, pathList: Array<any>, propsDict: { [key: string]: any }) => {
      console.log('資料匯出');
      exportFileParms.sourceKeys = [];
      exportFileParms.exportParmsList = [];
      commonProcessParms(mode, groupNameList, pathList, propsDict);
      // 對應搜尋的參數處理
      exportFileParms.exportParmsList.forEach((exportItem: any) => {
        Object.keys(exportItem.queryParamsMap).forEach((exportSearchParams: any) => {
          let fieldName = exportItem.queryParamsMap[exportSearchParams].fieldName;
          let fieldType = exportItem.queryParamsMap[exportSearchParams].paramType
          let parmData = resultParms.allDynaimcParms[fieldName]
          exportItem.params[exportSearchParams] = null;
          // 轉為陣列
          if (fieldType?.includes('Tuple') && !Array.isArray(parmData)) {
            parmData = [parmData];
          }
          // 轉為單值
          if (!fieldType && Array.isArray(parmData)) {
            parmData = parmData[0];;
          }
          exportItem.params[exportSearchParams] = parmData;
        });
      });
    },
    dynamic_run: () => {
      return dynamic_search_event.value
    },
    dynamic_stop: () => {
      dynamic_search_event.value = false
      return dynamic_search_event.value
    }
  }

  // vue3 inject() can only be used inside setup() or functional components
  /**
   * 初始Input Select選單
   * @param userField 元件的FieldName
   */
  const initListEvent = (userField: string) => {
    return new Promise((resolve) => {
      const eventListener = (data: any) => {
        if (data.field === userField) {
          emitter.off('initInputSelectList', eventListener);
          resolve(data);
        }
      };

      emitter.on('initInputSelectList', eventListener);
    });
  };

  /**
   * 預設值/更新值處理
   * 在pageView(初始) & Input(初始+更新)使用，Button元件(tab)不使用(因為不需要異步)
   * text、number:直接使用
   * date、daterange:日期時間格式轉換
   * radio、checkbox、tab:確認存在於選項中
   * select:確認是否符合選單選項
   * @param mode              模式(pageInit、inputInit、editorWatch、modelWatch)
   * @param propsDict         props物件
   * @param FieldName         使用者輸入名稱
   * @param keyName           FieldName Key(分辨是否為起始或結束時間)
   * @param URLParms          URL參數
   * @param fieldValue        預設值/更新值
   * @param typeDefaultData   不同型態的的資料(for editorWatch)
   * @returns                 parms: resultParmDate, typeData: typeData
   */
  const processParmsValue = async (mode: string, propsDict: { [key: string]: any; }, FieldName: string, keyName: string, URLParms: string | Array<string> | null, fieldValue: string | Array<string> | null, typeDefaultData: any) => {
    let updateData = URLParms ?? fieldValue;    // 更新值(URL參數優先)
    let updateDataList = updateData;            // 更新值(陣列)
    if (updateData && typeof updateData === 'string') { updateDataList = updateData.split(',') } // 更新陣列值轉換

    // Select選單資料
    let typeData: any;
    // 處理參數值資料
    let resultParmDate: { [key: string]: any; } = {
      inputType: propsDict.type,
      inputValue: null,
      isMultiple: false,
    };

    // 根據不同的Input類型進行處理
    switch (propsDict.type) {
      case 'text':
      case 'number':
        resultParmDate.inputValue = updateData;
        break;

      case 'date':
      case 'daterange':
        resultParmDate.inputType = keyName;
        // 是否有時間選擇器
        let enabletimePicker = propsDict.enabletimePicker ?? false;
        // 取得日期資料
        let date = updateDataList?.[0];
        if ((mode === 'editorWatch' || mode === 'modelWatch') && keyName === 'eDayfieldName') {
          date = updateDataList?.[1];
        }
        // 取得預設限制時間
        let defaultStartTime = propsDict.dateRangeSetting?.defaultStartTime ?? '00:00:00';
        let defaultEndTime = propsDict.dateRangeSetting?.defaultEndTime ?? '00:00:00';
        let defaultTime = keyName === 'fieldName' || keyName === 'sDayfieldName' ? defaultStartTime : defaultEndTime;
        // 處理日期時間格式
        let processData = processDate(date, defaultTime, enabletimePicker)
        resultParmDate.inputValue = processData.data;
        break;

      case 'radio':
        if (propsDict.optionsSetting) {
          if (typeof updateData === 'string' && updateData.includes(',')) {
            updateDataList = updateData.split(',')
          } else {
            let valueList = propsDict.optionsSetting.find((item: any) => item.value === updateData)?.value;
            resultParmDate.inputValue = valueList;
          }
        }
        break;

      case 'checkbox':
        resultParmDate.inputValue = propsDict?.optionsSetting.filter((item: any) => updateDataList?.includes(item.value)).map((item: any) => item.value);
        resultParmDate.isMultiple = true;
        break;

      case 'tab':
        if (propsDict.optionsSetting) {
          if (propsDict.multipleSelect) {
            let findTabDict = propsDict.optionsSetting.filter((item: any) => updateDataList?.includes(item.value));
            resultParmDate.inputValue = findTabDict.map((item: any) => item.value);
            resultParmDate.isMultiple = true;
          } else {
            Object.keys(propsDict.optionsSetting).forEach((item: any) => {
              const optionsSetting = propsDict.optionsSetting[item].value;
              // if (Array.isArray(updateDataList) && updateDataList.length > 0) {
              //   updateData = updateDataList[0];
              // }
              if (Array.isArray(updateDataList)) {
                if (updateDataList.length > 1) {
                  updateData = updateDataList;
                } else if (updateDataList.length === 1) {
                  updateData = updateDataList[0];
                }
              }

              if (JSON.stringify(optionsSetting) === JSON.stringify(updateData)) {
                let findTabDict = propsDict.optionsSetting[item];
                resultParmDate.inputValue = findTabDict.value;
              }
            })
          }
        }
        break;

      case 'select':
        const selectParmsDict: any = await processSelectParmsValue(mode, propsDict, updateData, URLParms, typeDefaultData)
        const selectDefaultDict = selectParmsDict.selectDefaultDict;
        typeDefaultData = selectParmsDict.typeDefaultData;

        typeData = {
          isMultiLayer: selectParmsDict.isMultiLayer,                  // 是否為多層選單
          isURLParm: URLParms ? true : false,                          // 是否使用URL參數
          isMultiple: propsDict.multipleSelect,                        // 是否為多選值
          processValue: selectDefaultDict?.value,                      // 預設值
          defaultCompleteKey: selectDefaultDict?.defaultCompleteKey,   // 預設值完整Key
          dataList: selectParmsDict.DataList,                          // 選單資料
          levelNumber: selectParmsDict.LevelNumber,                    // 選單層數
        }

        resultParmDate.inputValue = selectDefaultDict?.value;
        resultParmDate.isMultiple = propsDict.multipleSelect;
        break;
    }

    const resultData = { parms: resultParmDate, typeData: typeData };
    return resultData
  };

  return {
    groupDataPath,
    temporaryParms,
    resultParms,
    dynamicParmNameChecker,
    exportFileParms,
    triggerParmsSymbol,
    infoParmsSymbol,
    commonProcessParms,
    mlEvent,
    processParmsValue
  }
});
```
```ts
~/D/g/mirle-dashboard-frontend > cat src/helpers/base.ts 
import { computed, inject, ref, nextTick, onUnmounted } from 'vue';
import { storeToRefs } from 'pinia';
import { flatten, unflatten } from './flat';
import { useI18n } from 'vue-i18n'
import type { AxiosStatic } from 'axios';
import type { Emitter } from "mitt"
import type { Events } from "@/types/events"
import { usePageTreeStore } from '@/stores/pageTree'
import { dynamicParmsStore } from "@/stores/dynamicParms";
import { useLoginUserStore } from "@/stores/loginuser";
import { useSnippetEditorStore } from "@/stores/snippetEditor";
import { useSysParamsStore } from "@/stores/systemParams";
import { useDynamicParameter } from "@/hooks/snippet/useDynamicParameter";
import snippetsProps from './snippetProps';
import useLocale from '@/hooks/useLocale';
import { useSnippetDefaultData } from "@/hooks/snippet/useSnippetDefaultData";

// 查詢資料格式
interface payloadDict {
  isDynamicData: boolean;
  getData: {
    key: string,
    params: { [key: string]: any }
    paginationParams: Pagination
  };
}

// 後端分頁資料
type PageInfo = Record<"page" | "pageSize", number>
type OrderInfo = Record<"orderBy" | "orderRule", string | undefined>
type Pagination = Partial<PageInfo> & Partial<OrderInfo>

export default (props: BaseSnippetProps) => {
  const snippetData = ref();
  const snippetDataLoading = ref();
  const snippetDataNowIsAutoRefresh = ref();
  const informationData = ref();
  const pageTreeStore = usePageTreeStore();
  const parmsStore = dynamicParmsStore();
  const axios = inject('axios') as AxiosStatic;
  const editorStore = useSnippetEditorStore();
  const pageInfo = ref<Array<Pagination>>([])
  // 動態參數
  const { resultParms, exportFileParms } = storeToRefs(parmsStore);
  const { updateSearchGroup, updateLinkedTemporaryParms } = useDynamicParameter();
  // SSO Token
  const loginUserStore = useLoginUserStore();
  // 系統參數
  const systemStore = useSysParamsStore()
  const { state } = storeToRefs(systemStore);
  // 語系
  const { locale } = useI18n({ useScope: 'global' });
  const localeHook = useLocale()
  const { snippetsDataMap } = useSnippetDefaultData();

  /**
   * 判斷使用全局或是個別資料的樣式設定
   */
  const snippetStyle = computed(() => {
    const result: any[] = [];
    if (props.dataSourceSetting && props.dataSourceSetting.length > 0) {
      const flatGlobalStyle: { [key: string]: any } = flatten(props.globalStyle);
      props.dataSourceSetting.forEach(dsSetting => {
        const flatDsStyle: { [key: string]: any } = flatten(dsSetting.style)
        for (let key in flatDsStyle) {
          if (flatDsStyle[key] !== null && flatDsStyle[key] !== undefined) {
            flatGlobalStyle[key] = flatDsStyle[key];
          }
        }
        result.push(unflatten(flatGlobalStyle));
      });
    }

    return result;
  })

  /**
   * 更新傳遞給後端的查詢資料陣列
   * @param mode              元件屬性名稱(dataSourceSetting|informationSourceSetting)
   * @param propsDict         元件屬性物件
   * @param informationParms  元件特定補充資訊參數
   * @param payloadList       整理後的查詢資料陣列
   * @returns                 payloadList(整理後的查詢資料陣列)
   */
  const updateSearchPayload = (mode: string, index: number, propsDict: { [key: string]: any }, informationParms: { [key: string]: any }, payloadList: Array<payloadDict>) => {
    // 取得查詢參數設定(資料來源或補充資料)
    let targetSettingDict = propsDict.dataSourceSetting[index];
    if (mode === 'informationSourceSetting') {
      targetSettingDict = propsDict.informationSourceSetting[index];
    }

    // 取得查詢參數值
    const getParmsInfo = !!(targetSettingDict && targetSettingDict.dataSource);
    if (getParmsInfo) {
      const { dataSource: DataSource, queryParameter: QueryParameter, isDynamicData } = targetSettingDict;

      let dict: payloadDict = {
        isDynamicData: isDynamicData,
        getData: { key: DataSource, params: {}, paginationParams: pageInfo.value[index] ?? {} }
      };

      // 處理查詢參數值
      if (QueryParameter) {
        Object.keys(QueryParameter).forEach((SQLParm: any) => {
          let UIFieldName = QueryParameter[SQLParm].fieldName;  // UI欄位名稱
          let UIFieldType = QueryParameter[SQLParm].paramType;  // 參數值型態
          let haveInputParm = false;                            // 是否有輸入參數
          let parmData: any;                                    // 參數值

          // 根據元件屬性名稱取得查詢參數值
          if (mode === 'informationSourceSetting') {
            if (QueryParameter[SQLParm].isInformation && informationParms[UIFieldName]) {
              haveInputParm = true;
              parmData = informationParms[UIFieldName];
            }else{
            haveInputParm = resultParms.value.allDynaimcParms.hasOwnProperty(UIFieldName);
            parmData = resultParms.value.allDynaimcParms[UIFieldName];
            resultParms.value.allDynaimcParms[propsDict.path] = dict.getData.paginationParams;
            }
          } else {
            if (UIFieldName === 'CustomValue') {
              // 自訂參數值
              parmData = QueryParameter[SQLParm].customValue;
              if (parmData) { haveInputParm = true; }
            } else {
              // 選擇參數值
              haveInputParm = resultParms.value.allDynaimcParms.hasOwnProperty(UIFieldName);
              parmData = resultParms.value.allDynaimcParms[UIFieldName];
              resultParms.value.allDynaimcParms[propsDict.path] = dict.getData.paginationParams;
            }
          }

          // 依據參數值資料型態進行轉值為字串(單值)或陣列(多值)
          if (haveInputParm) {
            const convertToArray = UIFieldType?.includes('Tuple') && !Array.isArray(parmData) && parmData;  // 字串轉陣列            
            const convertToString = !UIFieldType?.includes('Tuple') && Array.isArray(parmData) && parmData; // 陣列轉字串
            parmData = convertToArray ? parmData.split(',') : convertToString ? parmData[0] : parmData;
            dict.getData.params[SQLParm] = parmData;
          } else {
            dict.getData.params[SQLParm] = null;
          }
        })
      }

      payloadList[index] = dict;
    }

    return payloadList
  };

  /**
   * 取得後端資料並更新元件資料(snippetData)
   * @param propsDict         元件屬性物件
   * @param payloadList       整理後的查詢資料陣列
   * @param isAutoRefreshData 是否為自動重整抓取資料
   * @returns                 allPromise Array<Promise<void>>
   */
  const updateData = (propsDict: { [key: string]: any }, payloadList: Array<payloadDict>, isAutoRefreshData: boolean = false) => {
    const allPromise: Promise<void>[] = [];

    payloadList?.forEach((item: { [key: string]: any }, index: number) => {
      const allPromise: Promise<void>[] = [];
      const paramsDict = Object.keys(item.getData.params);

      // 參數值是否有值(Data Source SQL Parameters必須都有選擇才能進行查詢)
      const hasKeyWithNull = Object.keys(item.getData.params).some((key) => item.getData.params[key] === null || item.getData.params[key] === undefined);
      const verifyDynamicParms = !!(
        !item.isDynamicData ||
        (item.isDynamicData && paramsDict.length > 0 && !hasKeyWithNull) // 若為動態資料，需要參數值不為空或無定義，且要有查詢值
      );

      // 取得後端資料
      if (verifyDynamicParms) {
        // 參數值去除頭尾空格
        paramsDict.forEach((key: any) => {
          if (typeof item.getData.params[key] === 'string') {
            item.getData.params[key] = item.getData.params[key].trim();
          }
        });

        // according to the number of data sources, set the loading status of the data source
        if (propsDict.dataSourceNumber > 1) {
          // multiple data source
          // give default list for initialization
          if (!snippetDataLoading.value) { snippetDataLoading.value = [] }
          if (!snippetDataNowIsAutoRefresh.value) { snippetDataNowIsAutoRefresh.value = [] }
          // set the loading status of the data source
          snippetDataLoading.value[index] = true;
          snippetDataNowIsAutoRefresh.value[index] = isAutoRefreshData;
        } else {
          // single data source
          snippetDataLoading.value = true;
          snippetDataNowIsAutoRefresh.value = isAutoRefreshData;
        }

        // get data from the backend
        const promise = axios.put("/dynamic_data/get_dynamic_data", item.getData).then((response: any) => {
          // set the data source value
          if (propsDict.dataSourceNumber > 1) {
            if (!snippetData.value) { snippetData.value = [] }
            snippetData.value[index] = response.data;
          } else {
            snippetData.value = response.data;
          }
        }).catch(function (error) {
          console.error(`Snippet Data Error: ${error}`);
        }).finally(() => {
          // make sure the loading status of the data source is updated
          if (propsDict.dataSourceNumber > 1) {
            // multiple data source
            snippetDataLoading.value[index] = false;
            snippetDataNowIsAutoRefresh.value[index] = false;
          } else {
            // single data source
            snippetDataLoading.value = false;
            snippetDataNowIsAutoRefresh.value = false;
          }
        });
        allPromise.push(promise)
      } else {
        console.error(`Incomplete data parameters.\n`, item);
      }
    })

    return allPromise
  };


  /**
   * 更新元件靜/動態資料
   * @param setIndex           資料集索引
   * @param isAutoRefreshData  是否為自動重整抓取資料
   * @returns                  allPromise Array<Promise<void>>
   * 1. 更新查詢群組(updateSearchGroup)
   * 2. 取得元件所有或特定資料集查詢參數(updateSearchPayload)
   * 3. 取得後端資料更新資料(updateData)
   */
  const getSnippetData = (setIndex: number | null, isAutoRefreshData: boolean = false) => {
    let payloadList: Array<payloadDict> = [];
    updateSearchGroup(props)

    const updateAllData = !!(setIndex === null && props.dataSourceNumber);      // 更新元件內所有資料來源
    const updateSpecificData = !!(setIndex != null && props.dataSourceSetting); // 更新元件特定資料來源(不同群組名稱處理)

    if (updateAllData) {
      for (let index = 0; index < (props.dataSourceNumber as number); index++) {
        payloadList = updateSearchPayload('dataSourceSetting', index, props, {}, payloadList);
      }
    } else if (updateSpecificData) {
      payloadList = updateSearchPayload('dataSourceSetting', setIndex, props, {}, payloadList);
    }

    return updateData(props, payloadList, isAutoRefreshData);
  };

  /** 匯出元件檔案 */
  const exportSnippetData = () => {
    /** 後端查詢資料整理 */
    /** 動&靜態資料查詢 */
    if (exportFileParms.value.exportParmsList.length > 0) {
      let getData = true;
      const baseURL = import.meta.env.VITE_BASE_URL ?? "";
      exportFileParms.value.exportParmsList.forEach((item: any, index: number) => {
        if (item.isDynamicData) {
          // 檢查動態查詢條件是否有符合所有查詢參數都有值
          let hasKeyWithNoValue = Object.keys(item.params).some((key) => !item.params[key]);
          // 動態查詢值必須有值才能進行查詢
          if (item.isDynamicData && hasKeyWithNoValue) {
            getData = false;
          }
          Object.keys(item.params).forEach((key: any) => {
            if (typeof item.params[key] === 'string') {
              let value = item.params[key].trim()
              item.params[key] = value
            }
          })
        }

        let exportPagination: Pagination = {
          page: 1,
          pageSize: -1,
          orderBy: resultParms.value.allDynaimcParms[item.exportItem].orderBy,
          orderRule: resultParms.value.allDynaimcParms[item.exportItem].orderRule
        }
        item['paginationParams'] = exportPagination
      })
      if (getData) {
        axios.post("/dynamic_data/export_dashboard_file", exportFileParms.value.exportParmsList).then((response: any) => {
          window.location.href = baseURL + response.data;
        }).catch(function (error) { });
      }
    }
  }

  /**
  * 更新補充資料
  * @param propsDict         元件屬性物件
  * @param informationParms  元件特定補充資訊參數
  */
  const getInformationData = async (propsDict: { [key: string]: any }, informationParms: { [key: string]: any }) => {
    let payloadList: Array<payloadDict> = [];
    const informationNumber = propsDict.informationSourceSetting?.length ?? 1;
    for (let index = 0; index < informationNumber; index++) {
      payloadList = updateSearchPayload('informationSourceSetting', index, propsDict, informationParms, payloadList);
    }

    if (payloadList.length > 0) {
      for (const item of payloadList) {
        // 參數值是否有值(Data Source SQL Parameters必須都有選擇才能進行查詢)
        let hasKeyWithNull = Object.keys(item.getData.params).some((key) => item.getData.params[key] === null || item.getData.params[key] === undefined);
        const isNotEmptyParms = item.isDynamicData && hasKeyWithNull ? false : true;

        // 參數值去除頭尾空格
        Object.keys(item.getData.params).forEach((key: any) => {
          if (typeof item.getData.params[key] === 'string') {
            item.getData.params[key] = item.getData.params[key].trim();
          }
        })

        // 取得後端資料
        if (isNotEmptyParms) {
          await axios.put("/dynamic_data/get_dynamic_data", item.getData).then((response: any) => {
            informationData.value = response.data;
          }).catch(function (error) {
            console.error(`Information Data Error: ${error}`);
          });
        } else {
          console.error(`Incomplete data parameters.\n`, item);
        }
      }
    }
  }

  // 更新資料來源時，更新元件資料
  const emitter = inject("emitter") as Emitter<Events>
  const updateDataSourceHandler = (event: Events['updateDataSource']) => {
    if (props.path === event.path) {
      nextTick(() => {
        getSnippetData(null);
      })
    }
  }
  emitter.on("updateDataSource", updateDataSourceHandler)

  // 更新查詢參數時，更新元件資料
  const updateResultParmsHandler = (event: Events['updateResultParms']) => {
    if (event.isTabInit) {
      // 切換TabLayout更新資料
      getSnippetData(null);
    } else if (!event.isInit) {
      event.dataUpdatePath?.forEach((pathToFind: string) => {
        if (props.path === pathToFind && event.searchGroup) {
          if (event.searchGroup.length > 0 && props.dataSourceSetting) {
            // button元件有指定更新的群組名稱(更新元件的特定資料來源)
            props.dataSourceSetting.forEach((set: any, setIndex: number) => {
              if (event.searchGroup) {
                event.searchGroup.forEach((item: any) => {
                  if (set.dataSourceGroupName === item) { getSnippetData(setIndex); }
                })
              }
            })
          } else {
            // button元件沒有指定更新的群組名稱(更新元件的所有資料來源)
            getSnippetData(null);
          }
        }
      })
    }
  }
  emitter.on("updateResultParms", updateResultParmsHandler)

  // 更新補充資料來源(需要點擊觸發更新元件資料)
  const updateInformationSourceHandler = (event: Events['updateInformationSource']) => { };
  emitter.on("updateInformationSource", updateInformationSourceHandler)

  // 銷毀
  onUnmounted(() => {
    emitter.off("updateDataSource", updateDataSourceHandler);
    emitter.off("updateResultParms", updateResultParmsHandler);
    emitter.off("updateInformationSource", updateInformationSourceHandler);
    intervalIds.value?.forEach((id: number) => { clearInterval(id) })
  })

  // 動靜態資料自動重整抓取資料
  const intervalIds = ref<any>([]);
  const refreshData = () => {
    const textcardWithoutDataSource = !!(
      editorStore.snippetType === 'textcard' &&
      editorStore.snippetProps.type === 'text' &&
      !editorStore.snippetProps.displaydataSource
    );
    const refresh = !!(props.dataSourceSetting && !pageTreeStore.isEditMode && !textcardWithoutDataSource);

    if (refresh) {
      props.dataSourceSetting!.forEach((set: { [key: string]: any }, setIndex: number) => {
        const refreshTimeSecond = set.dataSourceRefreshTime ?? state.value.sysParams.get('refresh_time')?.value ?? 300;
        const intervalId = (() => {
          return setInterval(() => {
            // console.log(`驗證測試:自動重整抓取資料，頻率${refreshTimeSecond}秒，第${setIndex + 1}個資料來源更新囉`);
            let isAutoRefreshData = true
            getSnippetData(setIndex, isAutoRefreshData);
          }, refreshTimeSecond * 1000);
        })();

        intervalIds.value.push(intervalId);
      });
    }
  };

  /**
   * 返回帶單位的屬性值
   * @param props屬性
   * @param 元件類型
   * @param 屬性
   */
  const getValueWithUnit = (value: number | string | undefined | null, snippetType: string, propMetaName: string): string | undefined => {
    if (!value && value !== 0) return "";

    const propMeta = snippetsProps[snippetType].find(prop => prop.name === propMetaName);

    if (propMeta!.uiLocation === 'globalStyle') {
      return value + props.unit!['style']
    } else {
      return value + props.unit![propMeta?.uiLocation!]
    }
  }

  /**
  * 處理超連結
  * @param props.hyperLink
  */
  const processHyperLink = async (link: any) => {
    let resultLink = '';

    if (link && link != '') {
      let processLink = link.trim();
      let urlWithHash = processLink.indexOf('#');
      const baseLink = urlWithHash > -1 ? processLink.substring(0, urlWithHash) : processLink;
      const hashText = urlWithHash > -1 ? processLink.substring(urlWithHash) : '';

      let prefix = baseLink.includes("?") ? '&' : '?';
      const resultLang = await localeHook.getLanguage();

      if (props.apiLink) {
        // 外網：API + Link + lang + ssoToken + (lang)
        const api = state.value.sysParams.get(props.apiLink)?.value;
        resultLink = baseLink.includes("lang=")
          ? api + baseLink + `${prefix}ssoToken=${loginUserStore.setToken}`
          : api + baseLink + `${prefix}ssoToken=${loginUserStore.setToken}&lang=${resultLang}`;
      } else {
        let urlWithLang = baseLink;

        // 內外網需要加語系
        if (!urlWithLang.includes("lang=")) {
          urlWithLang = baseLink + `${prefix}lang=${locale.value}`;
        }

        if (urlWithLang.includes("http")) {
          // 外網(不使用系統API或List、Alarm、NewsTicker item.link)
          let wanPrefix = urlWithLang.includes("?") ? '&' : '?';
          resultLink = urlWithLang + `${wanPrefix}ssoToken=${loginUserStore.setToken}`;
        } else {
          resultLink = urlWithLang;
        }
      }

      // move hash text to the end of external link
      resultLink = resultLink + hashText;
      resultLink = resultLink.trim();
    }

    return resultLink
  }

  /**
  * 觸發超連結，非編輯模式才會跳轉
  */
  const clickHyperLink = async (hyperLink: any) => {
    if (!pageTreeStore.isEditMode && hyperLink && hyperLink != '') {
      let openWindow = props.targetSelf ? '_self' : '_blank';
      let linkURL = await processHyperLink(hyperLink);

      window.open(linkURL, openWindow);
    }
  }

  /**
   * 帶有超連結元件滑鼠變形
   * @param props.hyperLink
   */
  const clickStyleCursor = (clickEvent: any) => {
    const result = clickEvent && clickEvent != 'noAction' ? 'pointer' : 'default';
    return { 'cursor': result } as any
  }

  /**
   * 資料格式警示
   * @param matchDataSource   是否不符合資料格式
   * @param snippetType       元件
   * @param dataSourceNumber  第幾個資料來源 
   * @param dataSourceKey     資料來源名稱
   */
  const snippetDataAlarm = (matchDataSource: boolean, snippetType: string, dataSourceNumber: number, dataSourceKey: string) => {
    let alarmMessage = {
      display: false,
      message: ''
    };

    if (!matchDataSource) {
      alarmMessage.message = `(${snippetType}) Incompatible Snippet Data Source : ${dataSourceKey}`;
      if (pageTreeStore.isEditMode) {
        alarmMessage.display = true;
      } else {
        console.error(alarmMessage.message)
      }
    }
    return alarmMessage
  }

  /**
   * 處理點擊事件
   * @param snippetType   元件類型
   * @param hyperLink     超連結
   * @param itemData      點擊資料(triggerLinkedData)
   */
  const baseClickEvent = (snippetType: string, hyperLink: string | null | undefined, itemData: any) => {
    //  Event : "noAction" | "hyperLink" | "itemHyperLink" | "triggerLinkedData" | "informationData" | null;

    if (props.clickEvent === 'hyperLink') {
      clickHyperLink(hyperLink)
    } else if (props.clickEvent === 'itemHyperLink') {
      clickHyperLink(itemData.link)
    } else if (props.clickEvent === 'triggerLinkedData') {
      let clickData = {};
      // clickData key 是選項名稱，要對應snippetsDataMap的linkParms
      switch (snippetsDataMap[snippetType].dataFrom) {
        case 'singleValue':
          if (snippetType === 'textcard') {
            // 僅開放第一個資料來源進行連動
            clickData = {
              'value': itemData[0]
            }
          } else {
            clickData = {
              'value': itemData
            }
          }
          break;
        case 'multipleData':
          clickData = {
            'legend': itemData?.datasetKey,
            'dataset raw value': itemData?.datasetValue,
          }
          break;
        case 'fullChartMode':
          if (snippetType === 'datagrid') {
            clickData = {
              // 待確認是否需要，因為目前查詢參數資料類型沒有物件類型
              // 'row dataset': itemData?.rowObject,           // 點選的該列所有資料 ex:{欄位名稱1:欄位1內容,欄位名稱2:欄位2內容}
              // 'column dataest': itemData?.columnObject,     // 點選的該欄資料 ex:{欄位名稱1:欄位1內容}
              'cell value': itemData?.columnObject?.value      // 點選的該資料 ex:欄位1內容
            }
          } else {
            clickData = {
              'legend': itemData?.datasetLabel,
              'label axis': itemData?.axisLabel,
              'dataset raw value': itemData?.datasetValue,
            }
          }
          break;
        case 'fullTableMode':
          if (snippetType === 'list') {
            clickData = {
              'item': itemData.item,
              'icon': itemData.icon,
              'link': itemData.link
            }
          } else if (snippetType === 'alarm' || snippetType === 'newsTicker') {
            clickData = {
              'title': itemData.title,
              'message': itemData.message,
              'link': itemData.link
            }
          } else if (snippetType === 'ganttbar') {
            clickData = {
              'status': itemData?.status,
              'start_time': itemData?.start_time,
              'start_time_ms': itemData?.start_time_ms,
              'end_time': itemData?.end_time,
              'end_time_ms': itemData?.end_time_ms,
              'dur': itemData?.dur,
            }
          } else if (snippetType === 'boxplot') {
            clickData = {
              'xLabels': itemData.label,
              'yValue': itemData.value
            }
          } else if (snippetType === 'polar') {
            clickData = {
              'key': itemData.key,
              'value': itemData.value
            }
          } else if (snippetType === 'tank') {
            clickData = {
              'tank ID': itemData.tank.tankID,
              'server ID': itemData.server.serverID,
              'server value': itemData.server.serverValue
            }
          } else if (snippetType === 'datagridServer') {
            clickData = {
              'cell value': itemData?.columnObject?.value
            }
          }
          break;
        default:
          break;
      }

      updateLinkedTemporaryParms(false, snippetType, props, clickData);
    }
  };


  return {
    snippetData,
    snippetDataLoading,
    snippetDataNowIsAutoRefresh,
    informationData,
    snippetStyle,
    getSnippetData,
    exportSnippetData,
    getInformationData,
    refreshData,
    emitter,
    getValueWithUnit,
    processHyperLink,
    clickHyperLink,
    clickStyleCursor,
    snippetDataAlarm,
    pageInfo,
    baseClickEvent,
  }
}
```

根據上面給的 codes, 幫我擷取出在 props.event==='export' 下點擊 Ml_button 中的 button 有關的部份, 然後詳細說明每段 codes 在做什麼事情

🤖:[ChatClaude-3-5-Sonnet]
好的,我會根據提供的代碼,提取出與 `props.event === 'export'` 相關的部分,並解釋每段代碼的功能。

首先,在 `Ml_button.vue` 文件中:

```javascript
case "export":
  const baseURL = import.meta.env.VITE_BASE_URL ?? "";
  parmsStore.mlEvent.export('export', groupNameList, [], props);
  exportSnippetData();
  break;
```

這段代碼做了以下幾件事:

1. 獲取基礎URL,如果環境變量中沒有定義,則使用空字符串。
2. 調用 `parmsStore.mlEvent.export` 方法,傳入 'export' 作為模式, `groupNameList` 作為群組名稱列表,空數組作為路徑列表,以及 `props` 作為屬性對象。
3. 調用 `exportSnippetData()` 方法。

接下來,在 `dynamicParms.ts` 文件中:

```javascript
export: (mode: string, groupNameList: any, pathList: Array<any>, propsDict: { [key: string]: any }) => {
  console.log('資料匯出');
  exportFileParms.sourceKeys = [];
  exportFileParms.exportParmsList = [];
  commonProcessParms(mode, groupNameList, pathList, propsDict);
  // 對應搜尋的參數處理
  exportFileParms.exportParmsList.forEach((exportItem: any) => {
    Object.keys(exportItem.queryParamsMap).forEach((exportSearchParams: any) => {
      let fieldName = exportItem.queryParamsMap[exportSearchParams].fieldName;
      let fieldType = exportItem.queryParamsMap[exportSearchParams].paramType
      let parmData = resultParms.value.allDynaimcParms[fieldName]
      exportItem.params[exportSearchParams] = null;
      // 轉為陣列
      if (fieldType?.includes('Tuple') && !Array.isArray(parmData)) {
        parmData = [parmData];
      }
      // 轉為單值
      if (!fieldType && Array.isArray(parmData)) {
        parmData = parmData[0];;
      }
      exportItem.params[exportSearchParams] = parmData;
    });
  });
}
```

這個方法做了以下幾件事:

1. 清空 `exportFileParms.sourceKeys` 和 `exportFileParms.exportParmsList`。
2. 調用 `commonProcessParms` 方法處理通用參數。
3. 遍歷 `exportFileParms.exportParmsList`,對每個導出項目進行參數處理:
   - 獲取字段名稱和字段類型
   - 從 `resultParms.value.allDynaimcParms` 中獲取參數數據
   - 根據字段類型進行數據轉換(數組或單值)
   - 將處理後的數據賦值給 `exportItem.params`

最後,在 `base.ts` 文件中:

```javascript
const exportSnippetData = () => {
  /** 後端查詢資料整理 */
  /** 動&靜態資料查詢 */
  if (exportFileParms.value.exportParmsList.length > 0) {
    let getData = true;
    const baseURL = import.meta.env.VITE_BASE_URL ?? "";
    exportFileParms.value.exportParmsList.forEach((item: any, index: number) => {
      if (item.isDynamicData) {
        // 檢查動態查詢條件是否有符合所有查詢參數都有值
        let hasKeyWithNoValue = Object.keys(item.params).some((key) => !item.params[key]);
        // 動態查詢值必須有值才能進行查詢
        if (item.isDynamicData && hasKeyWithNoValue) {
          getData = false;
        }
        Object.keys(item.params).forEach((key: any) => {
          if (typeof item.params[key] === 'string') {
            let value = item.params[key].trim()
            item.params[key] = value
          }
        })
      }

      let exportPagination: Pagination = {
        page: 1,
        pageSize: -1,
        orderBy: resultParms.value.allDynaimcParms[item.exportItem].orderBy,
        orderRule: resultParms.value.allDynaimcParms[item.exportItem].orderRule
      }
      item['paginationParams'] = exportPagination
    })
    if (getData) {
      axios.post("/dynamic_data/export_dashboard_file", exportFileParms.value.exportParmsList).then((response: any) => {
        window.location.href = baseURL + response.data;
      }).catch(function (error) { });
    }
  }
}
```

這個方法執行以下操作:

1. 檢查是否有需要導出的參數列表。
2. 遍歷 `exportFileParms.value.exportParmsList`,對每個項目進行處理:
   - 如果是動態數據,檢查所有參數是否都有值
   - 對字符串類型的參數進行去空格處理
   - 設置分頁參數
3. 如果所有檢查都通過(`getData` 為 true),則發送 POST 請求到 "/dynamic_data/export_dashboard_file"
4. 請求成功後,使用返回的數據更新當前頁面的 URL

總的來說,這個過程涉及參數的收集、處理和驗證,然後發送到後端進行文件導出,最後通過更新頁面 URL 來觸發文件下載。


💬:
理論上 'export' 需達到的功能是『會觸發匯出功能，將此頁面上BarLine、Pie、DataGrid、DataGrid Server元件的資料整合為一個Excel檔案匯出』
但是目前輸出的 excel 中的內容資料有錯誤 (會少某些關鍵欄位或者僅輸出 JSON 字串而非轉換成 excel 格式), 我應該在哪些部份加入 console.log 進行測試?

🤖:[ChatClaude-3-5-Sonnet]
根據您的描述，問題可能出現在數據處理或後端響應的過程中。為了更好地定位問題，我建議在以下幾個關鍵點添加 `console.log`：

1. 在 `Ml_button.vue` 中：

```javascript
case "export":
  console.log('Export triggered', { groupNameList, props });
  const baseURL = import.meta.env.VITE_BASE_URL ?? "";
  parmsStore.mlEvent.export('export', groupNameList, [], props);
  exportSnippetData();
  break;
```

2. 在 `dynamicParms.ts` 的 `export` 函數中：

```javascript
export: (mode: string, groupNameList: any, pathList: Array<any>, propsDict: { [key: string]: any }) => {
  console.log('Export function called', { mode, groupNameList, pathList, propsDict });
  console.log('Before processing', JSON.stringify(exportFileParms.value, null, 2));
  // ... existing code ...
  exportFileParms.exportParmsList.forEach((exportItem: any) => {
    console.log('Processing export item', JSON.stringify(exportItem, null, 2));
    // ... existing code ...
  });
  console.log('After processing', JSON.stringify(exportFileParms.value, null, 2));
}
```

3. 在 `base.ts` 的 `exportSnippetData` 函數中：

```javascript
const exportSnippetData = () => {
  console.log('exportSnippetData called');
  if (exportFileParms.value.exportParmsList.length > 0) {
    console.log('Export params list', JSON.stringify(exportFileParms.value.exportParmsList, null, 2));
    // ... existing code ...
    exportFileParms.value.exportParmsList.forEach((item: any, index: number) => {
      console.log(`Processing item ${index}`, JSON.stringify(item, null, 2));
      // ... existing code ...
    });
    if (getData) {
      console.log('Sending export request', JSON.stringify(exportFileParms.value.exportParmsList, null, 2));
      axios.post("/dynamic_data/export_dashboard_file", exportFileParms.value.exportParmsList).then((response: any) => {
        console.log('Export response', response.data);
        window.location.href = baseURL + response.data;
      }).catch(function (error) {
        console.error('Export error', error);
      });
    } else {
      console.log('getData is false, export cancelled');
    }
  } else {
    console.log('No items to export');
  }
}
```

這些 `console.log` 語句將幫助您跟踪：

1. 導出功能的觸發
2. 傳遞給導出函數的參數
3. 導出參數列表的處理過程
4. 最終發送到後端的數據
5. 後端的響應或錯誤

通過檢查這些日誌，您應該能夠確定：

- 是否所有需要導出的元件（BarLine、Pie、DataGrid、DataGrid Server）的數據都被正確收集
- 數據處理過程中是否有遺漏或錯誤
- 發送到後端的數據格式是否正確
- 後端是否正確響應請求

如果問題出在後端，您可能需要檢查後端代碼，特別是 `/dynamic_data/export_dashboard_file` 端點的實現。確保它正確處理接收到的數據，並生成適當格式的 Excel 文件。

此外，您可能還需要檢查 `exportFileParms` 的初始化過程，確保它包含了所有需要導出的元件的信息。這可能涉及到 `updateSearchPayload` 或其他相關函數的檢查。


💬:
我在 template 放置 ml_button 與 ml_barline 後點擊 'export' button 得到 console 如下:
```sh
Ml_button.vue:107
Export triggered {
    "groupNameList": [],
    "props": {
        "unit": {
            "base": "%"
        },
        "type": "basic",
        "dataSourceNumber": 0,
        "dataSourceSetting": [],
        "globalStyle": {},
        "queryEvent": {
            "groupName": []
        },
        "buttonSize": "large",
        "event": "export",
        "path": "root.m3f64sw9",
        "targetSelf": false,
        "marquee": false,
        "multipleSelect": false
    }
}
dynamicParms.ts:356
Export function called {
    "mode": "export",
    "groupNameList": [],
    "pathList": [],
    "propsDict": {
        "unit": {
            "base": "%"
        },
        "type": "basic",
        "dataSourceNumber": 0,
        "dataSourceSetting": [],
        "globalStyle": {},
        "queryEvent": {
            "groupName": []
        },
        "buttonSize": "large",
        "event": "export",
        "path": "root.m3f64sw9",
        "targetSelf": false,
        "marquee": false,
        "multipleSelect": false
    }
}
dynamicParms.ts:357 Before processing undefined
dynamicParms.ts:358 資料匯出
dynamicParms.ts:364 Processing export item {
  "key": null,
  "dashboardType": "barline",
  "dataGroupName": "",
  "params": {},
  "exportItem": "root.m3memhyh"
}
hook.js:608 [Vue warn]: Unhandled error during execution of native event handler 
  at <VBtn key=1 unit= {base: '%'} type="basic"  ... > 
  at <MlButton key=2 unit= {base: '%'} type="basic"  ... > 
  at <Wrapper ref="wrapper" padding="" margin=""  ... > 
  at <Snippet node= {id: 'm3f64sw9', path: 'root.m3f64sw9', snippetType: 'button', wrapperProps: {…}, snippetProps: {…}, …} containerHeight=59.9375 > 
  at <GridItem key="root.m3f64sw9" breakpointCols= {lg: 12, md: 10, sm: 6, xs: 4, xxs: 2} colNum=1  ... > 
  at <GridLayout layout= (2) [{…}, {…}] onUpdate:layout=fn col-num=1  ... > 
  at <Wrapper ref="wrapper" onClick=fn onDrop=fn<handleDrop>  ... > 
  at <Snippet node= {id: 'root', path: 'root', snippetType: 'layout', wrapperProps: {…}, snippetProps: {…}, …} key=14 containerHeight=959 > 
  at <BaseTransition appear=false persisted=false mode=undefined  ... > 
  at <Transition key=1 name="layout-fade" > 
  at <PageView pathMatch= ['test_event'] onVnodeUnmounted=fn<onVnodeUnmounted> ref=Ref< Proxy(Object) {__v_skip: true} > > 
  at <RouterView> 
  at <VMain class="customeSet" > 
  at <VApp key=0 > 
  at <App>
overrideMethod @ hook.js:608
warn$1 @ chunk-LRTCPMTY.js?v=005a197b:129
logError @ chunk-LRTCPMTY.js?v=005a197b:270
handleError @ chunk-LRTCPMTY.js?v=005a197b:262
callWithErrorHandling @ chunk-LRTCPMTY.js?v=005a197b:215
callWithAsyncErrorHandling @ chunk-LRTCPMTY.js?v=005a197b:220
callWithAsyncErrorHandling @ chunk-LRTCPMTY.js?v=005a197b:230
invoker @ chunk-LRTCPMTY.js?v=005a197b:8906
Show 8 more frames
Show less
chunk-LRTCPMTY.js?v=005a197b:275 Uncaught TypeError: Cannot convert undefined or null to object
    at Function.keys (<anonymous>)
    at dynamicParms.ts:365:16
    at Proxy.forEach (<anonymous>)
    at Proxy.export (dynamicParms.ts:363:39)
    at clickEvent (Ml_button.vue:109:30)
    at chunk-LRTCPMTY.js?v=005a197b:8924:60
    at callWithErrorHandling (chunk-LRTCPMTY.js?v=005a197b:213:19)
    at callWithAsyncErrorHandling (chunk-LRTCPMTY.js?v=005a197b:220:17)
    at callWithAsyncErrorHandling (chunk-LRTCPMTY.js?v=005a197b:230:17)
    at HTMLButtonElement.invoker (chunk-LRTCPMTY.js?v=005a197b:8906:5)
(anonymous) @ dynamicParms.ts:365
export @ dynamicParms.ts:363
clickEvent @ Ml_button.vue:109
(anonymous) @ chunk-LRTCPMTY.js?v=005a197b:8924
callWithErrorHandling @ chunk-LRTCPMTY.js?v=005a197b:213
callWithAsyncErrorHandling @ chunk-LRTCPMTY.js?v=005a197b:220
callWithAsyncErrorHandling @ chunk-LRTCPMTY.js?v=005a197b:230
invoker @ chunk-LRTCPMTY.js?v=005a197b:8906
Show 5 more frames
Show less
```

🤖:[ChatClaude-3-5-Sonnet]
根據您提供的控制台輸出，我們可以看到一些關鍵問題：

1. `exportFileParms.value` 是 `undefined`：
   在 `dynamicParms.ts:357` 中，我們看到 "Before processing undefined"，這表明 `exportFileParms.value` 是未定義的。

2. 嘗試對 `null` 使用 `Object.keys()`：
   錯誤消息 "Cannot convert undefined or null to object" 出現在 `dynamicParms.ts:365:16`，這表明我們嘗試對 `null` 或 `undefined` 值使用 `Object.keys()`。

3. `exportFileParms.exportParmsList` 中的項目缺少某些預期的屬性：
   在 `dynamicParms.ts:364` 中，我們可以看到處理的導出項目缺少 `isDynamicData` 和 `queryParamsMap` 屬性。

基於這些觀察，我建議進行以下檢查和修改：

1. 確保 `exportFileParms` 正確初始化：
   在 `dynamicParms.ts` 中，檢查 `exportFileParms` 的初始化。確保它被正確定義為一個響應式對象，例如：

   ```typescript
   const exportFileParms = ref<exportFileItems>({
     isHaveGroup: false,
     exportParmsList: [],
     sourceKeys: []
   });
   ```

2. 在處理導出項目之前添加空值檢查：
   在 `dynamicParms.ts` 的 `export` 函數中，添加一些防護代碼：

   ```typescript
   export: (mode: string, groupNameList: any, pathList: Array<any>, propsDict: { [key: string]: any }) => {
     console.log('Export function called', { mode, groupNameList, pathList, propsDict });
     if (!exportFileParms.value) {
       console.error('exportFileParms.value is undefined');
       return;
     }
     console.log('Before processing', JSON.stringify(exportFileParms.value, null, 2));
     // ... existing code ...
     exportFileParms.value.exportParmsList.forEach((exportItem: any) => {
       if (!exportItem || !exportItem.queryParamsMap) {
         console.error('Invalid export item', exportItem);
         return;
       }
       console.log('Processing export item', JSON.stringify(exportItem, null, 2));
       // ... existing code ...
     });
   }
   ```

3. 檢查 `exportFileParms` 的填充邏輯：
   確保在調用 `export` 函數之前，`exportFileParms` 已經被正確填充。這可能涉及檢查 `updateSearchPayload` 函數或其他相關的數據準備邏輯。

4. 檢查 BarLine 組件的數據源設置：
   確保 BarLine 組件有正確的數據源設置，包括 `dataSourceSetting`、`queryParameter` 等。這些信息應該在 `exportProcessParms` 函數中被正確處理。

5. 添加更多日誌：
   在 `base.ts` 的 `exportProcessParms` 函數中添加日誌，以確保它正確處理了 BarLine 組件：

   ```typescript
   const exportProcessParms = (element: any) => {
     console.log('Processing element for export', element);
     // ... existing code ...
   };
   ```

6. 檢查 `commonProcessParms` 函數：
   確保這個函數正確處理了導出情況，並正確填充了 `exportFileParms`。

通過這些修改和額外的日誌，您應該能夠更好地追踪問題的根源。特別注意 BarLine 組件的數據是如何被收集和處理的，因為根據您的描述，這些數據應該被包含在導出中，但目前似乎沒有被正確處理。


💬:
我懷疑是 export: 中的 commonProcessParms(mode, groupNameList, pathList, propsDict); 有問題, 我增加 console.log 檢查其輸出輸入如下:

```ts
    export: (mode: string, groupNameList: any, pathList: Array<any>, propsDict: { [key: string]: any }) => {
      console.log('資料匯出');
      exportFileParms.sourceKeys = [];
      exportFileParms.exportParmsList = [];

      // 檢查 input 資料
      console.log('input-groupNameList', JSON.stringify(groupNameList, null, 2));
      console.log('input-pathList', JSON.stringify(pathList, null, 2));
      console.log('input-propsDict', JSON.stringify(propsDict, null, 2));

      commonProcessParms(mode, groupNameList, pathList, propsDict);

      // 檢查 output 資料
      console.log('exportFileParms', JSON.stringify(exportFileParms, null, 2));

      exportFileParms.exportParmsList.forEach((exportItem: any) => {
        console.log('Processing export item', JSON.stringify(exportItem, null, 2));
        Object.keys(exportItem.queryParamsMap).forEach((exportSearchParams: any) => {
          let fieldName = exportItem.queryParamsMap[exportSearchParams].fieldName;
          let fieldType = exportItem.queryParamsMap[exportSearchParams].paramType
          let parmData = resultParms.allDynaimcParms[fieldName]
          exportItem.params[exportSearchParams] = null;

          if (fieldType?.includes('Tuple') && !Array.isArray(parmData)) {
            parmData = [parmData];
          }

          if (!fieldType && Array.isArray(parmData)) {
            parmData = parmData[0];;
          }
          exportItem.params[exportSearchParams] = parmData;
        });
      });
      console.log('exportFileParms(after-forEach)', JSON.stringify(exportFileParms, null, 2));
    },
```
```sh
得到 console 如下:
資料匯出
dynamicParms.ts:359 input-groupNameList []
dynamicParms.ts:360 input-pathList []
dynamicParms.ts:361 input-propsDict {
  "unit": {
    "base": "%"
  },
  "type": "basic",
  "dataSourceNumber": 0,
  "dataSourceSetting": [],
  "globalStyle": {},
  "queryEvent": {
    "groupName": []
  },
  "buttonSize": "large",
  "event": "export",
  "path": "root.m3f64sw9",
  "targetSelf": false,
  "marquee": false,
  "multipleSelect": false
}
dynamicParms.ts:363 exportFileParms {
  "isHaveGroup": false,
  "exportParmsList": [
    {
      "key": null,
      "dashboardType": "barline",
      "dataGroupName": "",
      "params": {},
      "exportItem": "root.m3memhyh"
    }
  ],
  "sourceKeys": [
    null
  ]
}
dynamicParms.ts:366 Processing export item {
  "key": null,
  "dashboardType": "barline",
  "dataGroupName": "",
  "params": {},
  "exportItem": "root.m3memhyh"
}
hook.js:608 [Vue warn]: Unhandled error during execution of native event handler 
  at <VBtn key=1 unit= {base: '%'} type="basic"  ... > 
  at <MlButton key=2 unit= {base: '%'} type="basic"  ... > 
  at <Wrapper ref="wrapper" padding="" margin=""  ... > 
  at <Snippet node= {id: 'm3f64sw9', path: 'root.m3f64sw9', snippetType: 'button', wrapperProps: {…}, snippetProps: {…}, …} containerHeight=59.9375 > 
  at <GridItem key="root.m3f64sw9" breakpointCols= {lg: 12, md: 10, sm: 6, xs: 4, xxs: 2} colNum=1  ... > 
  at <GridLayout layout= (2) [{…}, {…}] onUpdate:layout=fn col-num=1  ... > 
  at <Wrapper ref="wrapper" onClick=fn onDrop=fn<handleDrop>  ... > 
  at <Snippet node= {id: 'root', path: 'root', snippetType: 'layout', wrapperProps: {…}, snippetProps: {…}, …} key=1 containerHeight=959 > 
  at <BaseTransition appear=false persisted=false mode=undefined  ... > 
  at <Transition key=1 name="layout-fade" > 
  at <PageView pathMatch= ['test_event'] onVnodeUnmounted=fn<onVnodeUnmounted> ref=Ref< Proxy(Object) {__v_skip: true} > > 
  at <RouterView> 
  at <VMain class="customeSet" > 
  at <VApp key=0 > 
  at <App>
overrideMethod @ hook.js:608
warn$1 @ chunk-LRTCPMTY.js?v=005a197b:129
logError @ chunk-LRTCPMTY.js?v=005a197b:270
handleError @ chunk-LRTCPMTY.js?v=005a197b:262
callWithErrorHandling @ chunk-LRTCPMTY.js?v=005a197b:215
callWithAsyncErrorHandling @ chunk-LRTCPMTY.js?v=005a197b:220
callWithAsyncErrorHandling @ chunk-LRTCPMTY.js?v=005a197b:230
invoker @ chunk-LRTCPMTY.js?v=005a197b:8906
Show 8 more frames
Show less
chunk-LRTCPMTY.js?v=005a197b:275 Uncaught TypeError: Cannot convert undefined or null to object
    at Function.keys (<anonymous>)
    at dynamicParms.ts:367:16
    at Proxy.forEach (<anonymous>)
    at Proxy.export (dynamicParms.ts:365:39)
    at clickEvent (Ml_button.vue:108:30)
    at chunk-LRTCPMTY.js?v=005a197b:8924:60
    at callWithErrorHandling (chunk-LRTCPMTY.js?v=005a197b:213:19)
    at callWithAsyncErrorHandling (chunk-LRTCPMTY.js?v=005a197b:220:17)
    at callWithAsyncErrorHandling (chunk-LRTCPMTY.js?v=005a197b:230:17)
    at HTMLButtonElement.invoker (chunk-LRTCPMTY.js?v=005a197b:8906:5)
```
似乎是在
```ts
      exportFileParms.exportParmsList.forEach((exportItem: any) => {
        console.log('Processing export item', JSON.stringify(exportItem, null, 2));
        Object.keys(exportItem.queryParamsMap).forEach((exportSearchParams: any) => {
          let fieldName = exportItem.queryParamsMap[exportSearchParams].fieldName;
          let fieldType = exportItem.queryParamsMap[exportSearchParams].paramType
          let parmData = resultParms.allDynaimcParms[fieldName]
          exportItem.params[exportSearchParams] = null;
          // 轉為陣列
          if (fieldType?.includes('Tuple') && !Array.isArray(parmData)) {
            parmData = [parmData];
          }
          // 轉為單值
          if (!fieldType && Array.isArray(parmData)) {
            parmData = parmData[0];;
          }
          exportItem.params[exportSearchParams] = parmData;
        });
      });
```
產生 error 造成 app crash

🤖:[ChatClaude-3-5-Sonnet]
您的分析是正確的。根據提供的日誌和錯誤信息，問題確實出現在您指出的代碼段中。具體來說，錯誤發生在嘗試使用 `Object.keys(exportItem.queryParamsMap)` 時，這表明 `exportItem.queryParamsMap` 是 `undefined` 或 `null`。

讓我們逐步解決這個問題：

1. 首先，添加一個安全檢查來避免應用崩潰：

```typescript
exportFileParms.exportParmsList.forEach((exportItem: any) => {
  console.log('Processing export item', JSON.stringify(exportItem, null, 2));
  
  if (!exportItem.queryParamsMap) {
    console.warn('queryParamsMap is undefined for export item:', exportItem);
    return; // Skip this iteration
  }

  Object.keys(exportItem.queryParamsMap).forEach((exportSearchParams: any) => {
    // ... rest of the code
  });
});
```

2. 接下來，我們需要理解為什麼 `queryParamsMap` 是未定義的。根據您提供的日誌，`exportItem` 看起來是這樣的：

```json
{
  "key": null,
  "dashboardType": "barline",
  "dataGroupName": "",
  "params": {},
  "exportItem": "root.m3memhyh"
}
```

這裡沒有 `queryParamsMap` 屬性。這可能意味著在 `commonProcessParms` 函數中，`queryParamsMap` 沒有被正確設置。

3. 檢查 `commonProcessParms` 函數：
   在 `commonProcessParms` 函數中，確保它正確地設置了 `queryParamsMap`。可能需要添加類似這樣的代碼：

```typescript
const commonProcessParms = (mode: string, groupNameList: any, pathList: Array<any>, propsDict: { [key: string]: any }) => {
  // ... existing code ...

  // 假設這是在某個地方設置 exportFileParms 的地方
  exportFileParms.exportParmsList.forEach(item => {
    if (!item.queryParamsMap) {
      item.queryParamsMap = {}; // 初始化為空對象，如果它不存在
    }
    // 這裡應該有邏輯來填充 queryParamsMap
  });

  // ... rest of the function
};
```

4. 檢查 `exportProcessParms` 函數：
   這個函數應該負責初始設置 `exportFileParms`。確保它正確地初始化了所有必要的屬性，包括 `queryParamsMap`。

5. 添加更多日誌：
   在 `commonProcessParms` 和 `exportProcessParms` 函數中添加更多日誌，以便跟踪 `exportFileParms` 的設置過程：

```typescript
const exportProcessParms = (element: any) => {
  console.log('exportProcessParms called with:', element);
  // ... existing code ...
  console.log('exportFileParms after processing:', JSON.stringify(exportFileParms, null, 2));
};

const commonProcessParms = (mode: string, groupNameList: any, pathList: Array<any>, propsDict: { [key: string]: any }) => {
  console.log('commonProcessParms called with:', { mode, groupNameList, pathList, propsDict });
  // ... existing code ...
  console.log('exportFileParms after commonProcessParms:', JSON.stringify(exportFileParms, null, 2));
};
```

6. 檢查 BarLine 組件的設置：
   確保 BarLine 組件有正確的數據源設置，包括必要的查詢參數。這些應該在某個地方被添加到 `exportFileParms.exportParmsList` 中。

通過這些步驟，您應該能夠:
1. 防止應用崩潰
2. 更好地理解 `queryParamsMap` 為什麼是未定義的
3. 找到並修復 `queryParamsMap` 沒有被正確設置的原因

完成這些更改後，再次運行導出功能，並檢查新的日誌輸出。這應該能幫助您確定問題的根源，並指導您進行必要的修復。


💬:
先假設 queryParamsMap 有不存在的情況是合理的, 所以我加入判斷讓它不存在時不去做 Object.keys 這段的處理如下:

```ts
    export: (mode: string, groupNameList: any, pathList: Array<any>, propsDict: { [key: string]: any }) => {
      console.log('資料匯出');
      exportFileParms.sourceKeys = [];
      exportFileParms.exportParmsList = [];
      console.log('input-groupNameList', JSON.stringify(groupNameList, null, 2));
      console.log('input-pathList', JSON.stringify(pathList, null, 2));
      console.log('input-propsDict', JSON.stringify(propsDict, null, 2));
      commonProcessParms(mode, groupNameList, pathList, propsDict);
      console.log('exportFileParms', JSON.stringify(exportFileParms, null, 2));
      exportFileParms.exportParmsList.forEach((exportItem: any) => {
        console.log('Processing export item', JSON.stringify(exportItem, null, 2));
        if (exportItem.queryParamsMap) {  // 如果 queryParamsMap 存在才做 Object.keys 處理
          Object.keys(exportItem.queryParamsMap).forEach((exportSearchParams: any) => {
            console.log('Processing exportSearchParams', JSON.stringify(exportSearchParams, null, 2));
            let fieldName = exportItem.queryParamsMap[exportSearchParams].fieldName;
            let fieldType = exportItem.queryParamsMap[exportSearchParams].paramType
            let parmData = resultParms.allDynaimcParms[fieldName]
            exportItem.params[exportSearchParams] = null;
            if (fieldType?.includes('Tuple') && !Array.isArray(parmData)) {
              parmData = [parmData];
            }
            if (!fieldType && Array.isArray(parmData)) {
              parmData = parmData[0];;
            }
            exportItem.params[exportSearchParams] = parmData;
          });
        }
      });
      console.log('exportFileParms(after-forEach)', JSON.stringify(exportFileParms, null, 2));
    },

```

修改後 console.log 輸出如下:
```sh
資料匯出
dynamicParms.ts:359 input-groupNameList []
dynamicParms.ts:360 input-pathList []
dynamicParms.ts:361 input-propsDict {
  "unit": {
    "base": "%"
  },
  "type": "basic",
  "dataSourceNumber": 0,
  "dataSourceSetting": [],
  "globalStyle": {},
  "queryEvent": {
    "groupName": []
  },
  "buttonSize": "large",
  "event": "export",
  "path": "root.m3f64sw9",
  "targetSelf": false,
  "marquee": false,
  "multipleSelect": false
}
dynamicParms.ts:363 exportFileParms {
  "isHaveGroup": false,
  "exportParmsList": [
    {
      "key": "MES.訂單生產台數",
      "isDynamicData": false,
      "dashboardType": "barline",
      "dataGroupName": "",
      "params": {},
      "queryParamsMap": {},
      "exportItem": "root.m3memhyh"
    }
  ],
  "sourceKeys": [
    "MES.訂單生產台數"
  ]
}
dynamicParms.ts:366 Processing export item {
  "key": "MES.訂單生產台數",
  "isDynamicData": false,
  "dashboardType": "barline",
  "dataGroupName": "",
  "params": {},
  "queryParamsMap": {},
  "exportItem": "root.m3memhyh"
}
dynamicParms.ts:386 exportFileParms(after-forEach) {
  "isHaveGroup": false,
  "exportParmsList": [
    {
      "key": "MES.訂單生產台數",
      "isDynamicData": false,
      "dashboardType": "barline",
      "dataGroupName": "",
      "params": {},
      "queryParamsMap": {},
      "exportItem": "root.m3memhyh"
    }
  ],
  "sourceKeys": [
    "MES.訂單生產台數"
  ]
}
base.ts:258 exportSnippetData called
base.ts:262 Export params list [
  {
    "key": "MES.訂單生產台數",
    "isDynamicData": false,
    "dashboardType": "barline",
    "dataGroupName": "",
    "params": {},
    "queryParamsMap": {},
    "exportItem": "root.m3memhyh"
  }
]
base.ts:266 Processing item 0 {
  "key": "MES.訂單生產台數",
  "isDynamicData": false,
  "dashboardType": "barline",
  "dataGroupName": "",
  "params": {},
  "queryParamsMap": {},
  "exportItem": "root.m3memhyh"
}
hook.js:608 [Vue warn]: Unhandled error during execution of native event handler 
  at <VBtn key=1 unit= {base: '%'} type="basic"  ... > 
  at <MlButton key=2 unit= {base: '%'} type="basic"  ... > 
  at <Wrapper ref="wrapper" padding="" margin=""  ... > 
  at <Snippet node= {id: 'm3f64sw9', path: 'root.m3f64sw9', snippetType: 'button', wrapperProps: {…}, snippetProps: {…}, …} containerHeight=59.9375 > 
  at <GridItem key="root.m3f64sw9" breakpointCols= {lg: 12, md: 10, sm: 6, xs: 4, xxs: 2} colNum=1  ... > 
  at <GridLayout layout= (2) [{…}, {…}] onUpdate:layout=fn col-num=1  ... > 
  at <Wrapper ref="wrapper" onClick=fn onDrop=fn<handleDrop>  ... > 
  at <Snippet node= {id: 'root', path: 'root', snippetType: 'layout', wrapperProps: {…}, snippetProps: {…}, …} key=1 containerHeight=959 > 
  at <BaseTransition appear=false persisted=false mode=undefined  ... > 
  at <Transition key=1 name="layout-fade" > 
  at <PageView pathMatch= ['test_event'] onVnodeUnmounted=fn<onVnodeUnmounted> ref=Ref< Proxy(Object) {__v_skip: true} > > 
  at <RouterView> 
  at <VMain class="customeSet" > 
  at <VApp key=0 > 
  at <App>
overrideMethod @ hook.js:608
warn$1 @ chunk-LRTCPMTY.js?v=005a197b:129
logError @ chunk-LRTCPMTY.js?v=005a197b:270
handleError @ chunk-LRTCPMTY.js?v=005a197b:262
callWithErrorHandling @ chunk-LRTCPMTY.js?v=005a197b:215
callWithAsyncErrorHandling @ chunk-LRTCPMTY.js?v=005a197b:220
callWithAsyncErrorHandling @ chunk-LRTCPMTY.js?v=005a197b:230
invoker @ chunk-LRTCPMTY.js?v=005a197b:8906
Show 8 more frames
Show less
chunk-LRTCPMTY.js?v=005a197b:275 Uncaught TypeError: Cannot read properties of undefined (reading 'orderBy')
    at base.ts:285:71
    at Proxy.forEach (<anonymous>)
    at exportSnippetData (base.ts:265:45)
    at clickEvent (Ml_button.vue:109:11)
    at chunk-LRTCPMTY.js?v=005a197b:8924:60
    at callWithErrorHandling (chunk-LRTCPMTY.js?v=005a197b:213:19)
    at callWithAsyncErrorHandling (chunk-LRTCPMTY.js?v=005a197b:220:17)
    at callWithAsyncErrorHandling (chunk-LRTCPMTY.js?v=005a197b:230:17)
    at HTMLButtonElement.invoker (chunk-LRTCPMTY.js?v=005a197b:8906:5)
```

現在雖然 Ml_button 中 parmsStore.mlEvent.export('export', groupNameList, [], props); 已經不會產生錯誤, 但是 exportSnippetData(); 產生錯誤, 似乎是在 base.ts:285:71 這行有問題

orderBy: resultParms.value.allDynaimcParms[item.exportItem].orderBy,

如果根據上面的 console 來看 item.exportItem 的值應該為 root.m3memhyh, 但是 resultParms.value.allDynaimcParms[root.m3memhyh] 為 undefined, 這樣就會產生錯誤, 於是我再增加一個 console.log 到 exportSnippetData 中檢查 resultParms.value.allDynaimcParms 的內容如下:

```ts
  const exportSnippetData = () => {
    console.log('exportSnippetData called');
    if (exportFileParms.value.exportParmsList.length > 0) {
      console.log('Export params list', JSON.stringify(exportFileParms.value.exportParmsList, null, 2));
      let getData = true;
      const baseURL = import.meta.env.VITE_BASE_URL ?? "";
      exportFileParms.value.exportParmsList.forEach((item: any, index: number) => {
        console.log(`Processing item ${index}`, JSON.stringify(item, null, 2));
        if (item.isDynamicData) {
          let hasKeyWithNoValue = Object.keys(item.params).some((key) => !item.params[key]);
          if (item.isDynamicData && hasKeyWithNoValue) {
            getData = false;
          }
          Object.keys(item.params).forEach((key: any) => {
            if (typeof item.params[key] === 'string') {
              let value = item.params[key].trim()
              item.params[key] = value
            }
          })
        }

        console.log('allDynaimcParms', JSON.stringify(resultParms.value.allDynaimcParms, null, 2)); // 檢查 allDynaimcParms 的內容
        let exportPagination: Pagination = {
          page: 1,
          pageSize: -1,
          orderBy: resultParms.value.allDynaimcParms[item.exportItem].orderBy,
          orderRule: resultParms.value.allDynaimcParms[item.exportItem].orderRule
        }
        item['paginationParams'] = exportPagination
      })
      if (getData) {
        console.log('Sending export request', JSON.stringify(exportFileParms.value.exportParmsList, null, 2));
        axios.post("/dynamic_data/export_dashboard_file", exportFileParms.value.exportParmsList).then((response: any) => {
          console.log('Export response', response.data);
          window.location.href = baseURL + response.data;
        }).catch(function (error) {
          console.error('Export error', error);
        });
      } else {
        console.log('getData is false, export cancelled');
      }
    } else {
      console.log('No items to export');
    }
  }
```

發現 allDynaimcParms {} , 所以 allDynaimcParms[root.m3memhyh] 會得到 undefined, 這樣造成後面 .orderBy 或者 .orderRule 都會產生錯誤 crash app

🤖:[ChatClaude-3-5-Sonnet]
您的分析非常準確。問題確實出在 `resultParms.value.allDynaimcParms` 是空對象，導致無法獲取 `orderBy` 和 `orderRule`。

為了解決這個問題，我們需要做以下幾個調整：

1. 首先，我們應該檢查 `allDynaimcParms` 是否有我們需要的數據，如果沒有，我們應該提供一個默認值。

2. 我們還應該檢查 `item.exportItem` 是否在 `allDynaimcParms` 中存在。

3. 如果這些值不存在，我們應該提供合理的默認值。

讓我們修改 `exportSnippetData` 函數如下：

```typescript
const exportSnippetData = () => {
  console.log('exportSnippetData called');
  if (exportFileParms.value.exportParmsList.length > 0) {
    console.log('Export params list', JSON.stringify(exportFileParms.value.exportParmsList, null, 2));
    let getData = true;
    const baseURL = import.meta.env.VITE_BASE_URL ?? "";
    exportFileParms.value.exportParmsList.forEach((item: any, index: number) => {
      console.log(`Processing item ${index}`, JSON.stringify(item, null, 2));
      if (item.isDynamicData) {
        let hasKeyWithNoValue = Object.keys(item.params).some((key) => !item.params[key]);
        if (item.isDynamicData && hasKeyWithNoValue) {
          getData = false;
        }
        Object.keys(item.params).forEach((key: any) => {
          if (typeof item.params[key] === 'string') {
            let value = item.params[key].trim()
            item.params[key] = value
          }
        })
      }

      console.log('allDynaimcParms', JSON.stringify(resultParms.value.allDynaimcParms, null, 2));
      
      // 檢查 allDynaimcParms 是否有數據，以及 item.exportItem 是否存在
      const dynamicParms = resultParms.value.allDynaimcParms[item.exportItem] || {};
      
      let exportPagination: Pagination = {
        page: 1,
        pageSize: -1,
        orderBy: dynamicParms.orderBy || undefined,  // 如果不存在，使用 undefined
        orderRule: dynamicParms.orderRule || undefined  // 如果不存在，使用 undefined
      }
      item['paginationParams'] = exportPagination
    })
    if (getData) {
      console.log('Sending export request', JSON.stringify(exportFileParms.value.exportParmsList, null, 2));
      axios.post("/dynamic_data/export_dashboard_file", exportFileParms.value.exportParmsList).then((response: any) => {
        console.log('Export response', response.data);
        window.location.href = baseURL + response.data;
      }).catch(function (error) {
        console.error('Export error', error);
      });
    } else {
      console.log('getData is false, export cancelled');
    }
  } else {
    console.log('No items to export');
  }
}
```

這個修改做了以下幾點：

1. 我們使用 `resultParms.value.allDynaimcParms[item.exportItem] || {}` 來獲取動態參數，如果不存在，則使用空對象。

2. 在設置 `orderBy` 和 `orderRule` 時，我們使用 `||` 操作符來提供一個默認值（在這種情況下是 `undefined`）。

3. 這樣即使 `allDynaimcParms` 是空的，或者 `item.exportItem` 不存在於 `allDynaimcParms` 中，代碼也不會崩潰，而是會使用默認值。

這樣修改後，應用程序不應該再因為這個問題而崩潰。但是，這可能揭示了一個更深層次的問題：為什麼 `allDynaimcParms` 是空的？這可能需要進一步調查：

1. 檢查 `resultParms` 是如何被填充的。
2. 確保在調用 `exportSnippetData` 之前，`allDynaimcParms` 已經被正確填充。
3. 檢查 `item.exportItem` 的值是否正確，它應該對應於 `allDynaimcParms` 中的一個鍵。

這些檢查可能會幫助您找到為什麼 `allDynaimcParms` 是空的根本原因，從而幫助您更好地解決這個問題。


💬:



