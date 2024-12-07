---
date: 2024-11-28
type: fact
aliases:
  -
hubs:
  - "[[Work]]"
---

# WORK: how to prevent re fetch data when entering edit mode

💬:
```vue
<!-- src/components/common/SideBar.vue  -->
<script lang="ts" setup>
import { ref, watch, inject, onUnmounted, computed, onMounted, onBeforeUnmount } from 'vue';
import SystemSettingMenu from './SystemSettingMenu.vue';
import SystemSettingBar from './SystemSettingBar.vue';
import PageMenu from './PageMenu.vue';
import { useLoginUserStore } from "@/stores/loginuser";
import { useSysParamsStore } from "@/stores/systemParams";
import { useMenuStore } from "@/stores/menu";
import { useRouter, useRoute } from 'vue-router';
import TemplateList from './TemplateList.vue';
import PropsSettingList from './PropsSettingList.vue';
import { usePageTreeStore } from "@/stores/pageTree";
import { useSnippetEditorStore } from "@/stores/snippetEditor";
import type { Axios } from 'axios';
import type { Emitter } from "mitt"
import type { Events } from "@/types/events"
import Swal from 'sweetalert2';
import { getCurrentFullTime } from "@/helpers/timeUtils";
import { useI18n } from 'vue-i18n'
import { usePageDataStore } from "@/stores/pageData";
import { useDialog } from "@/hooks/useDialog";
import Dialog from "@/components/common/Dialog.vue";
import useLocale from '@/hooks/useLocale';
import type { Locale } from '@/hooks/useLocale';
import UserMenu from './UserMenu.vue';
import screenfull from "@/plugins/screenfull";

const loginUserStore = useLoginUserStore();
const sysParamsStore = useSysParamsStore()
const menuStore = useMenuStore();
const snippetEditorStore = useSnippetEditorStore();
const pageTreeStore = usePageTreeStore()
const axios = inject("axios") as Axios;

const { t, locale, availableLocales } = useI18n({ useScope: 'global' });
const localeHook = useLocale()
const pageDataStore = usePageDataStore();

const langItems = computed(() => {
  if (!sysParamsStore.state.sysParams.get("available_lang"))
    return availableLocales

  return availableLocales.filter(locale => {
    return sysParamsStore.state.sysParams.get("available_lang")?.value.includes(locale)
  });
})

const displayInformationIcon = computed(() => {
  const sysHandbook = sysParamsStore.state.sysParams.get("available_handbook");
  if (!sysHandbook) {
    console.error("available_handbook not found in sysParams")
  }

  return sysHandbook?.value ?? false;
})

const {
  show: alertDialog,
  showDialog,
  hideDialog,
} = useDialog();

// 頁面管理資料更動時更新pageDataList
const emitter = inject("emitter") as Emitter<Events>
const updatePageDataHandler = (event: Events['updatePageData']) => {
  pageDataStore.pageDataList = event.getPageDataList!;
}
emitter.on("updatePageData", updatePageDataHandler)

// 元件銷毀時取消事件監聽，防止持續監聽
onUnmounted(() => {
  emitter.off("updatePageData", updatePageDataHandler)
})

const drawer = ref(false)
const userItems = ref([
  {
    text: "Logout",
    icon: "mdi-logout",
  },
])

const router = useRouter();
const handleHomeClick = () => {
  //window.location.href = "/"
  router.push({ path: '/' });
}

const handleHandBookClick = () => {
  window.open(import.meta.env.VITE_HANDBOOK_BASE_URL + '/', '_blank');
}

const logOutClick = async () => {
  let returnstatus = (await axios.get(`sso/logout`)).data
  let requesthref = encodeURI(location.href);
  let loginUrl = import.meta.env.VITE_SSO_BASE_URL
  if (returnstatus)
    window.location.href = loginUrl + "/?redirectUrl=" + requesthref
}

const handleEdit = async (isSave: boolean) => {
  if (isSave) {
    let message = "";
    try {
      const response = await axios.patch(`page/${pageTreeStore.pageId}`, {
        content: JSON.stringify(pageTreeStore.tree),
      });
      message = "儲存成功";
    } catch {
      message = "儲存失敗";
    }
    Swal.fire(message)
  } else {
    pageTreeStore.tree = pageTreeStore.treeBackup;
    pageTreeStore.treeBackup = null;
  }
  // 檢視模式
  pageTreeStore.isEditMode = false;
  pageTreeStore.isEditSnippetMode = false;
}
watch(() => pageTreeStore.isEditMode, (newVal) => {
  if (newVal) {
    // 保存原來page，讓取消編輯時可以還原
    pageTreeStore.treeBackup = JSON.parse(JSON.stringify(pageTreeStore.tree));
  }

  drawer.value = newVal;
  // 重繪grid layout RWD才不會出問題
  pageTreeStore.updateKey++;
})

watch(() => pageTreeStore.isEditSnippetMode, (newVal) => {
  if (!newVal) {
    // 取消編輯中元件陰影
    snippetEditorStore.openedPath = null;
  }
})

const route = useRoute();

const isEqptPages = computed(() => {
  return ['/edm/detail', '/edm/menu', '/ipm/detail', '/ipm/menu'].includes(route.path)
})

const handleMenuItemClicked = () => {
  pageTreeStore.isEditMode = false;
  pageTreeStore.isEditSnippetMode = false;
  drawer.value = false;
}

const time = ref("");
setInterval(() => {
  time.value = getCurrentFullTime();
}, 1000)

async function changeLocale(localeValue: Locale) {
  await localeHook.updateLocale(localeValue)
  const currentQuery = route.query
  const newQuery = { lang: localeValue }
  const mergedQuery = { ...currentQuery, ...newQuery }

  if (route.name === 'page') {
    const langAlert = pageDataStore.langExistAlert(pageDataStore.pageDataList, localeValue, route.path)
    //若無對應語系網頁彈出警示窗
    alertDialog.value = langAlert || false;
    // 頁面跳轉，原本網址參數(若無語言參數)加入語言參數
    router.push({ path: route.path, query: currentQuery });
    if (!langAlert) { router.push({ path: route.path, query: mergedQuery }); }
  } else {
    router.push({ path: route.path, query: mergedQuery });
  }
}
const showFloatGroup = ref(false)
const mouseEnterEvent = () => {
  showFloatGroup.value = true;
}
const mouseLeaveEvent = () => {
  showFloatGroup.value = false;
}
onMounted(() => {
  let header = document.querySelector("header");
  if (header) {
    header.addEventListener("mouseenter", mouseEnterEvent);
    header.addEventListener("mouseleave", mouseLeaveEvent);
  }
  if (screenfull.isEnabled) {
    // 註冊監聽全銀幕觸發切換事件
    screenfull.on('change', change);
  }
})
onBeforeUnmount(() => {
  let header = document.querySelector("header");
  if (header) {
    header.removeEventListener("mouseenter", mouseEnterEvent);
    header.removeEventListener("mouseleave", mouseLeaveEvent);
  }
  if (screenfull.isEnabled) {
    // 移除註冊監聽全銀幕觸發切換事件
    screenfull.off('change', change);
  }
})
const change = () => {
  if (screenfull.isFullscreen) {
    // 全螢幕時隱藏header
    document.querySelector('header')?.classList.add('d-none');
    // 全螢幕時main不須padding-top
    document.querySelector('main')?.classList.add('pt-0');
    // 全螢幕時記錄drawer狀態
    screenfull.beforeNavState = drawer.value;
    // 全螢幕時drawer隱藏
    drawer.value = false;
  } else {
    // 非全螢幕時顯示header
    document.querySelector('header')?.classList.remove('d-none');
    // 非全螢幕時main須padding-top
    document.querySelector('main')?.classList.remove('pt-0');
    // 非全螢幕時還原drawer狀態
    if (screenfull.beforeNavState !== undefined && screenfull.beforeNavState !== null) {
      drawer.value = screenfull.beforeNavState;
    }
  }
}
</script>

<template>
  <v-navigation-drawer v-model="drawer" permanent width="300">
    <PropsSettingList v-if="pageTreeStore.isEditMode && pageTreeStore.isEditSnippetMode"></PropsSettingList>
    <TemplateList v-else-if="pageTreeStore.isEditMode"></TemplateList>
    <template v-else>
      <v-card class="navLogo d-flex justify-center mt-4 flex-column align-center">
        <v-img width="95" height="100%" contain :src="sysParamsStore.state.sysParams.get('logo')?.value || ''"></v-img>
        <p class="text-white text-center text-caption">Realtime Data Center</p>
      </v-card>
      <v-list class="userInfo">
        <v-list-item class="navUser" prepend-icon="mdi-account-circle" :title="loginUserStore.loginUser.username"
          :subtitle="loginUserStore.roleNameShow">
          <v-tooltip activator="parent" location="bottom">{{ loginUserStore.roleNameShow }}</v-tooltip>
          <template v-slot:append>
            <UserMenu />
          </template>
        </v-list-item>
      </v-list>
      <v-divider></v-divider>
      <page-menu />
    </template>
  </v-navigation-drawer>
  <v-app-bar prominent height="50">
    <div v-show="!pageTreeStore.isEditMode" style="display:flex;">
      <v-app-bar-nav-icon class="mx-2" variant="text" @click.stop="drawer = !drawer">
        <svg :class="{ 'hamburger': true, 'active': drawer }" viewBox="0 0 100 100" width="35">
          <path
            d="m 20,33 h 40 c 0,0 8.5,-0.68551 8.5,10.375 0,8.292653 -6.122707,9.002293 -8.5,6.625 l -11.071429,-11.071429" />
          <path d="m 70,50 h -30" />
          <path
            d="m 20,67 h 40 c 0,0 8.5,0.68551 8.5,-10.375 0,-8.292653 -6.122707,-9.002293 -8.5,-6.625 l -11.071429,11.071429" />
        </svg>
        <v-tooltip activator="parent" location="bottom">{{ t('sidebar.menu') }}</v-tooltip>
      </v-app-bar-nav-icon>
      <v-divider vertical class="mr-2"></v-divider>
      <v-btn variant="text" icon @click="handleHomeClick">
        <v-icon icon="mdi-home"></v-icon>
        <v-tooltip activator="parent" location="bottom">{{ t('sidebar.home') }}</v-tooltip>
      </v-btn>
      <v-btn v-if="displayInformationIcon" variant="text" icon @click="handleHandBookClick">
        <v-icon icon="mdi-information-outline"></v-icon>
        <v-tooltip activator="parent" location="bottom">{{ t('sidebar.handbook') }}</v-tooltip>
      </v-btn>
      <v-divider vertical class="mx-2"></v-divider>
    </div>
    <template v-if="menuStore.currentFunc || isEqptPages">
      <p class="text-subtitle-2 ml-3 mr-5" v-if="!isEqptPages">{{ menuStore.currentFuncType?.name }}</p>
      <system-setting-bar v-if="!isEqptPages" />
    </template>
    <template v-else>
      <template v-if="menuStore.funcs.length > 0 && menuStore.funcTypes.length > 0 && menuStore.ishomePage != true">
        <template v-if="pageTreeStore.isEditMode">
          <v-btn variant="text" icon @click="handleEdit(true)" class="ml-2 mr-1">
            <v-icon icon="mdi-upload" size="xs-small"></v-icon>
            <v-tooltip activator="parent" location="bottom">{{ t('common.button.save') }}</v-tooltip>
          </v-btn>
          <v-divider vertical class="mx-1"></v-divider>
          <v-btn variant="text" icon @click="handleEdit(false)" class="mx-1">
            <v-icon icon="mdi-close" size="small"></v-icon>
            <v-tooltip activator="parent" location="bottom">{{ t('common.button.close') }}</v-tooltip>
          </v-btn>
          <v-divider vertical class="mx-1"></v-divider>
        </template>
        <template v-else>
          <v-btn variant="text" icon @click="pageTreeStore.isEditMode = true">
            <v-icon icon="mdi-pencil"></v-icon>
            <v-tooltip activator="parent" location="bottom">{{ t('sidebar.edit') }}</v-tooltip>
          </v-btn>
          <v-divider vertical class="ml-2"></v-divider>
        </template>
      </template>
      <template v-if="!pageTreeStore.isEditMode">
        <div>
          <v-breadcrumbs :items="menuStore.breadcrumb" divider="╱">
          </v-breadcrumbs>
        </div>
        <div v-if="showFloatGroup" class="float-group">
          <v-btn variant="text" icon="mdi-fullscreen" color="white" size="x-small"
            @click="screenfull.switchScreenfullBody">
          </v-btn>
        </div>
      </template>
    </template>
    <v-spacer></v-spacer>
    <p class="text-caption mr-2">
      <font-awesome-icon icon="fa-solid fa-clock" />
      {{ time }}
    </p>
    <template v-if="!pageTreeStore.isEditMode">
      <div class="d-flex align-center">
        <v-divider vertical class="mx-1"></v-divider>
        <v-select class="flex-grow-0" :model-value="locale" @update:model-value="changeLocale($event)"
          :items="langItems" hide-details density="compact" variant="solo" rounded="xl" bg-color="rgba(0, 0, 0, 0)">
        </v-select>
        <v-divider vertical class="mx-1"></v-divider>
        <v-btn icon="mdi-plus" class="ml-1 mr-2" v-if="menuStore.funcs.length > 0 && menuStore.funcTypes.length > 0"
          variant="text">
          <v-icon icon="mdi-cog"></v-icon>
          <v-tooltip activator="parent" location="bottom">{{ t('sidebar.setting') }}</v-tooltip>
          <SystemSettingMenu @close:menu="handleMenuItemClicked"></SystemSettingMenu>
        </v-btn>
      </div>
    </template>
  </v-app-bar>
  <Dialog v-model:show="alertDialog" width="320px" :title="t('common.dialog.langNotExist')" is-alert>
    <template #actionBgGrey>
      <v-btn variant="flat" @click="hideDialog" color="white">{{ t('common.button.confirm') }}</v-btn>
    </template>
  </Dialog>
</template>
```

上面是一個 side bar component, 其中
```vue
          <v-btn variant="text" icon @click="pageTreeStore.isEditMode = true">
            <v-icon icon="mdi-pencil"></v-icon>
            <v-tooltip activator="parent" location="bottom">{{ t('sidebar.edit') }}</v-tooltip>
          </v-btn>
```
這個button被點擊時會觸發 PageView.vue 上載入的每個 Ml_ snippet components 重新載入去執行 getSnippetData 導致再次 fetch 資料

```vue
<!-- src/views/PageView.vue  -->

<script setup lang="ts">
import { inject, ref, watch, onMounted } from 'vue';
import Snippet from '@/components/common/Snippet.vue'
import type { AxiosStatic } from 'axios';
import { usePageTreeStore } from '@/stores/pageTree'
import { useRoute, useRouter } from 'vue-router';
import { getRootLayoutTemplate } from "@/helpers/tree"
import { useI18n } from 'vue-i18n'
import { usePageDataStore } from "@/stores/pageData";
import elementResizeDetectorMaker from 'element-resize-detector'
import { useDynamicParameter } from "@/hooks/snippet/useDynamicParameter";
import useLocale from '@/hooks/useLocale';
import { DotLoader } from "vue3-spinner"
import { useSnippetParmeter } from "@/hooks/snippet/useSnippetParmeter";


const localeHook = useLocale()
const { pageDataList, langExistAlert } = usePageDataStore();
const { getUrlParams, getPageSreachParms, initPageResultParms } = useDynamicParameter();
const { getSnippetGeneralParams } = useSnippetParmeter();

const axios = inject('axios') as AxiosStatic;
const router = useRouter();
const route = useRoute();
const props = defineProps<{
  pathMatch?: Array<string> | string;
}>()
const pageTreeStore = usePageTreeStore()
const pageDataStore = usePageDataStore()

// 取得該頁面網址的查詢參數
const urlParamsDict = getUrlParams(router)

const getPageContent = async (lang: any) => {
  const params: any = {};
  let getAPI: boolean = true;
  let requestUrl = "/page";
  params["lang"] = lang;
  if (props.pathMatch === undefined) {
    // url為"/"時會進到這個condition
    try {
      const homeMenuSystemParam = (await axios.get("/sys", {
        params: {
          key: "home_menu"
        }
      })).data[0];
      const homeMenu = (await axios.get(`/menu/${homeMenuSystemParam.value}`)).data;
      requestUrl = `/page/${homeMenu.page_id}`;
    } catch {
      router.push({ name: "NotFound" });
    }
  } else if (Array.isArray(props.pathMatch)) {
    // 若無該語系談出警示視窗且頁面不抓取API
    getAPI = langExistAlert(pageDataList, params["lang"], '/page/' + params["url"]);
    params["url"] = '/' + props.pathMatch.join('/');
  } else {
    params["url"] = "";
  }

  if (getAPI) {
    axios.get(requestUrl, {
      params: params
    }).then(async (response) => {
      const page = props.pathMatch ? response.data[0] : response.data;
      const content = page.content ? JSON.parse(page.content) : getRootLayoutTemplate()
      // 取得Snippet的一般參數設定(預設值)
      await getSnippetGeneralParams()
      // 取得頁面查詢參數屬性(props)
      const parmDict: { [key: string]: any; } = {};
      getPageSreachParms(parmDict, (content as TreeNode), false)
      // 初始查詢參數
      await initPageResultParms(parmDict, urlParamsDict, false)
      pageTreeStore.pageId = page.id;
      pageTreeStore.tree = content;
      pageTreeStore.updateKey++;
      pageDataStore.isLoading = false;
    }).catch(() => {
      router.push({ name: "NotFound" });
    })
  }
};

watch(() => props.pathMatch, async () => {
  await init()
}, {
  deep: true
})

const rootContainer = ref(null)
const rootContainerClientHeight = ref(0)
var erd = elementResizeDetectorMaker({
  strategy: "scroll"
});
onMounted(async () => {
  await init()

  erd.listenTo(rootContainer.value!, (element) => {
    rootContainerClientHeight.value = element.clientHeight;
  })
})

document.addEventListener('mousedown', (e) => {
  if ((e.target as HTMLElement).classList.contains("vue-resizable-handle")) {
    pageTreeStore.isResizing = true;
  }
})

document.addEventListener('mouseup', (e) => {
  pageTreeStore.isResizing = false;
})

async function init() {
  const urlLang = await localeHook.getLanguage()
  getPageContent(urlLang);
}
</script>

<template>
  <div id="rootContainer" ref="rootContainer" class="h-100 w-100 d-flex flex-column">
    <div v-if="pageDataStore.isLoading" class="spinner-container">
      <DotLoader />
    </div>
    <Transition name="layout-fade" v-else>
      <Snippet v-if="pageTreeStore.tree" :node="pageTreeStore.tree" :key="pageTreeStore.updateKey"
        :containerHeight="rootContainerClientHeight">
      </Snippet>
    </Transition>
  </div>
</template>

```
下面以 Ml_textcard.vue 為範例:

```vue
~/D/g/mirle-dashboard-frontend > cat src/components/snippets/Ml_textcard.vue 
<script setup lang="ts">
import { computed, type StyleValue } from "vue";
import createBaseMixin from "@/helpers/base";
import type { SnippetBasicProps, SnippetTooltipProps, SnippetDataColorProps } from "@/types/types";
import { useSnippetDefaultData } from "@/hooks/snippet/useSnippetDefaultData";
import { useSnippetDefaultSetting } from "@/hooks/snippet/useSnippetDefaultSetting";
import Snackbar from '@/components/common/Snackbar.vue';
import { useSnackbar } from "@/hooks/useSnackbar";
import SnippetTooltip from "@/components/common/SnippetTooltip.vue"
import { flatten } from '@/helpers/flat';

const { snippetsDataFormat, mappingValueColor } = useSnippetDefaultData();
const { defaultSnippetStyle, defaultTextPadding } = useSnippetDefaultSetting();
const { show: isShowSnackbar, text: snackbarText, showSnackbar } = useSnackbar();

interface StyleAttribute {
  data?: string[];
  title?: string[];
  unit?: string[];
  descriptionTitle?: string[];
  description?: string[];
}

const props = defineProps<SnippetBasicProps & SnippetTooltipProps & SnippetDataColorProps & {
  // options props
  type?: string;
  subType?: string;
  displaydataSource?: boolean;
  cardAlignItems?: string;
  cardAlignContent?: string;
  cardJustifyContent?: string;
  titleCardDirection?: string;
  lineBorder?: string;
  lineColor?: string;
  titleJustifyContent?: string;
  textJustifyContent?: string;
  // 已棄用屬性(先前頁面元件有此屬性，仍先保留避免出現警告)
  contentDescribeFontSize?: any;
  dataSrouceFontWeight?: any;
  dataSourceRefreshTime?: any;
  basicCardJustifyContent?: any;
  titleFontWeight?: any;
  textFontColor?: any;
  textAlign?: any;
  titleFontSize?: any;
  secondUnitFontSize?: any;
  textFontWeight?: any;
  // dataset
  dataSourceSetting: Array<{
    isNumberData?: boolean;
    contentList?: StyleAttribute;
    dynamicColorToggle?: boolean;
    style: {
      fontWeightList?: StyleAttribute;
      fontSizeList?: StyleAttribute;
      fontColorList?: StyleAttribute;
    };
  }>;
  globalStyle: {
    fontWeightList?: StyleAttribute;
    fontSizeList?: StyleAttribute;
    fontColorList?: StyleAttribute;
  }
}>();

const {
  snippetData,
  getSnippetData,
  refreshData,
  getValueWithUnit,
  clickHyperLink,
  clickStyleCursor,
  snippetDataAlarm,
  baseClickEvent
} = createBaseMixin(props);

// init snippetData
getSnippetData(null);
refreshData();

const defaultStyle = defaultSnippetStyle('snippet_textcard');
const selectedType = computed(() => { return props.type ?? 'text' });
const styleUnit = computed(() => { return props.unit?.style ?? 'rem' });
const isTimeSubType = computed(() => { return !!(props.subType === 'time_unit') });
const templateTooltip = computed(() => { return props.tooltipSetting });

/**
 * 內容文字初始設定
 * 細節詳見 components/snippet_options/TextCardSetting.vue
 * @param 第幾個資料來源
 */
function initContentList(datasetList: any) {
  if (datasetList.contentList === undefined) datasetList.contentList = {}
  let kind = ['data', 'title', 'unit', 'descriptionTitle', 'description'];
  kind.forEach((el: string) => {
    if (!datasetList.contentList[el]) { datasetList.contentList[el] = [] }
  })
}

/**
 * 樣式初始設定
 * 細節詳見 components/snippet_options/TextCardStyle.vue
 * @param 第幾個資料來源
 */
function initStyleList(dict: any) {
  let styleList = ['fontWeightList', 'fontSizeList', 'fontColorList'];
  let kind = ['title', 'data', 'unit', 'descriptionTitle', 'description'];

  styleList.forEach((item: string) => {
    if (dict.length != styleList.length) {
      if (!dict[item]) { dict[item] = {}; }
      kind.forEach((el: string) => {
        if (!dict[item][el]) { dict[item][el] = [] }
      })
    }
  })
}

// 初始
initStyleList(props.globalStyle);
for (let index = 0; index < Number(props.dataSourceNumber); index++) {
  initStyleList(props.dataSourceSetting[index].style)
  initContentList(props.dataSourceSetting[index])
};


/** 資料 */
const templateDataset = computed(() => {

  // ---資料來源驗證，已轉換TimeUnit數值  
  let usedData: { [key: string]: any } = [];
  if (snippetData.value) {
    const dataVerification = snippetsDataFormat.textCard(snippetData.value, props.dataSourceNumber, props.type, props.subType);
    // 判斷值是否為數值
    dataVerification.isNumber.forEach((element: boolean, index: number) => {
      let singleData = !!(props.type === 'text' || props.type === 'basic_card' || props.type === 'title_card');
      let multiData = !!(props.type === 'compare_card' || props.type === 'description_card');

      if (singleData && index === 0) {
        props.dataSourceSetting![0].isNumberData = element;
      }

      if (multiData) {
        props.dataSourceSetting![index].isNumberData = element;
      }
    })

    dataVerification.match.forEach((element: boolean, index: number) => {
      if (!element) {
        // 資料來源條件錯誤驗證
        let dataKey = props.dataSourceSetting?.[index].dataSource;
        let alarmMessage = snippetDataAlarm(element, 'TextCard', index + 1, dataKey)
        if (alarmMessage.display) { showSnackbar(alarmMessage.message) }
      } else {
        usedData[index] = dataVerification.data[index];
      }
    })
  }

  // ---預設內容
  const usedTimeSubType = !!(
    isTimeSubType.value &&
    (props.type != 'text' && props.type != 'description_card')
  );

  const defaultText = defaultStyle.text;
  const defaultData = {
    title: defaultStyle.text.title,
    data: { data1: defaultText.data, data2: defaultText.data },
    unit: {
      prefix: usedTimeSubType ? defaultText.timePrefix : defaultText.prefix,
      backdrop: usedTimeSubType ? defaultText.timeBackdrop : defaultText.backdrop
    },
    description: { title: defaultText.description.title, content: defaultText.description.content },
  };

  let resultData = Array.from({ length: props.dataSourceNumber }, () => ({
    ...defaultData,
    unit: props.type === 'description_card' ?
      { prefix: defaultText.description.prefix, backdrop: defaultText.description.prefix } : defaultData.unit,
  }));

  // ---資料內容
  if (usedData.length > 0) {
    usedData.forEach((element: any, index: any) => {
      if (element) {
        if (props.type === 'text' || props.type === 'description_card') {
          resultData[index].data = { data1: element.value, data2: defaultText.data };
        } else {
          if (isTimeSubType.value) {
            resultData[index].data = { data1: element.hours, data2: element.minutes };
          } else {
            resultData[index].data = { data1: element.value, data2: defaultText.data };
          }
        }
      }
    });
  }

  // ---文字內容
  props.dataSourceSetting.forEach((set, setIndex) => {
    // console.log(set.contentList)
    const contentList: any = set.contentList;
    if (contentList) {
      Object.keys(contentList).forEach(item => {
        switch (item) {
          case 'title':
          case 'descriptionTitle':
          case 'description':
            if (contentList[item][0] != undefined) {
              (resultData as any)[setIndex][item] = contentList[item]?.[0];
            }
            break;
          case 'unit':
            if (contentList[item][0] != undefined) {
              resultData[setIndex].unit = { prefix: contentList[item][0], backdrop: resultData[setIndex].unit.backdrop }
            }
            if (contentList[item][1] != undefined) {
              resultData[setIndex].unit = { prefix: resultData[setIndex].unit.prefix, backdrop: contentList[item][1] }
            }
            break;
          case 'data':
            if (selectedType.value === 'text' && !props.displaydataSource && contentList[item][0] != undefined) {
              (resultData as any)[setIndex].data = { data1: contentList[item][0], data2: resultData[setIndex].data.data2 };
            }
            break;
          default:
            break;
        }
      });
    }
  });


  return resultData
});


/** 樣式 */
/**
 * 文字樣式設定
 * @param setIndex  資料集索引值
 * @param attribute template位置屬性
 * attribute:       title | data | prefix | backdrop | descriptionTitle | description
 * datasetMode:     title | data | unit | descriptionTitle | description
 * styleMode:       title | data | unit
 */
const templateStyle = (setIndex: number, attribute: string) => {
  // ---預設樣式
  const fontStyle = defaultStyle.fontStyle;
  const listIndex = attribute === 'backdrop' ? 2 : 1;
  let styleMode = attribute;
  let datasetMode = attribute;
  let isStyleUnit = !!(attribute != 'title' && attribute === 'data');
  let isdatasetUnit = !!(attribute === 'prefix' || attribute === 'backdrop');
  let paddingValue = defaultTextPadding;

  if (isStyleUnit) { styleMode = 'unit'; }
  if (isdatasetUnit) { datasetMode = 'unit'; }
  if (selectedType.value === 'text') { styleMode = 'title'; }

  let styleDict: { [key: string]: any } = {
    'fontWeightList': fontStyle.weight ?? null,
    'fontSizeList': fontStyle.size[styleMode] ?? null,
    'fontColorList': fontStyle.color ?? null
  };

  // ---更新樣式
  const datasetStyle = flatten(props.dataSourceSetting[setIndex]) as Record<string, any>;
  const globalStyle = flatten(props.globalStyle) as Record<string, any>;

  // 根據有無前後綴字調整padding
  const contentPreBack = datasetStyle[`contentList.${datasetMode}.${listIndex - 1}`];
  if (contentPreBack && contentPreBack === '') { paddingValue = '0px' };

  Object.keys(styleDict).forEach((datasetItem: string) => {
    const datasetItemStyle = datasetStyle[`style.${datasetItem}.${datasetMode}.${listIndex - 1}`];

    if (datasetItemStyle) {
      styleDict[datasetItem] = datasetItemStyle;
    } else {
      let check = !!(
        datasetMode === 'title' ||
        (datasetMode === 'unit' && selectedType.value === 'description_card')
      )
      let globalIndex = check ? 0 : listIndex - 1;
      const globalItemStyle = globalStyle[`${datasetItem}.${datasetMode}.${globalIndex}`];

      if (globalItemStyle) {
        styleDict[datasetItem] = globalItemStyle;
      }
    }
  })

  // ---樣式結果
  const reslutStyle: StyleValue = {
    'font-size': `${styleDict.fontSizeList}${styleUnit.value}`,
    'line-height': `${styleDict.fontSizeList}${styleUnit.value}`,
    'font-weight': styleDict.fontWeightList,
    'color': styleDict.fontColorList,
    'padding-left': paddingValue,
    'padding-right': paddingValue,
  }

  // ---資料顏色變更
  /**
   * 資料套用顏色優先順序為閥值顏色、資料集個別顏色、全域顏色。
   * 若資料(皆)為數值才會顯示閥值顏色設定選項。
   * 若有開啟dynamicColorToggle，編輯面板的資料集個別顏色、全域顏色選項將會消失。
   */
  let useDataColor = false;
  if (attribute === 'data') {
    if (props.dataSourceSetting[0].dynamicColorToggle) {
      const textCardMultiData = !!(selectedType.value === 'compare_card' || selectedType.value === 'description_card');
      const textCardIsNumberData = props.dataSourceSetting![0]!.isNumberData;

      if (textCardMultiData) {
        // Check if all data in the dataset are numbers
        const isAllNumberData = props.dataSourceSetting!.every(setting => setting.isNumberData);
        useDataColor = isAllNumberData
      } else if (textCardIsNumberData) {
        // 單筆資料時，需資料為數字時，才顯示閥值顏色設定
        useDataColor = textCardIsNumberData;
      }
    } else {
      // 使用資料集或全域字體顏色
      reslutStyle.color = styleDict.fontColorList
    }
  }

  // 資料閥值顏色更新
  if (useDataColor && snippetData.value?.[setIndex]) {
    const numberData = Number(snippetData.value[setIndex])
    let dynamicColor = mappingValueColor('number', numberData, props.dataSourceSetting[0])
    if (dynamicColor) { reslutStyle.color = dynamicColor }
    styleDict.fontColorList = reslutStyle.color
  }

  return reslutStyle
}

const cardDirection = computed(() => {
  if (props.titleCardDirection != 'column') {
    return { 'flex-direction': 'row', 'align-items': 'baseline' } as StyleValue
  } else {
    return { 'flex-direction': 'column' } as StyleValue
  }
});
const compareLine = computed(() => {
  return {
    'height': '100%',
    'border-right': `${getValueWithUnit(props.lineBorder, "textcard", "lineBorder")} solid ${props.lineColor ?? "#FFF"}`
  }
});
const textAlign = computed(() => { return { 'align-items': `${props.cardAlignItems ?? 'center'}` } });              // 文字垂直對
齊方向
const alignContent = computed(() => { return { 'align-content': `${props.cardAlignContent ?? 'center'}` } });       // 容器垂直對
齊方向
const justifyContent = computed(() => { return { 'justify-content': `${props.cardJustifyContent ?? 'center'}` } }); // 容器水平對
齊方向
const justifyTitle = computed(() => { return { 'justify-content': `${props.titleJustifyContent ?? 'center'}` } });  // 標題文字水
平方向
const justifyText = computed(() => { return { 'justify-content': `${props.textJustifyContent ?? 'center'}` } });    // 內容文字水
平方向
</script>

<template>
  <SnippetTooltip snippet="textcard" :tooltipSetting="templateTooltip"></SnippetTooltip>
  <div class="outer-layer-textcard" :style="clickStyleCursor(props.clickEvent)"
    @click="baseClickEvent('textcard', props.hyperLink, snippetData)">
    <span v-if="selectedType === 'basic_card'">
      <span class="basic-card" :style="[alignContent, justifyContent]">
        <span v-if="isTimeSubType" :style="templateStyle(0, 'data')">{{ templateDataset[0].data.data2 }}</span>
        <span :style="templateStyle(0, 'prefix')">{{ templateDataset[0].unit.prefix }}</span>
        <span :style="templateStyle(0, 'data')">{{ templateDataset[0].data.data1 }}</span>
        <span :style="templateStyle(0, 'backdrop')">{{ templateDataset[0].unit.backdrop }}</span>
      </span>
    </span>
    <span v-else-if="selectedType === 'title_card'" class="title-crad">
      <span class="title-card-body" :style="cardDirection">
        <span :style="[justifyTitle, alignContent]">
          <span :style="templateStyle(1, 'title')">{{ templateDataset[0].title }}</span>
        </span>
        <span :style="[justifyText, alignContent]">
          <span v-if="isTimeSubType" :style="templateStyle(0, 'data')">{{ templateDataset[0].data.data2 }}</span>
          <span :style="templateStyle(0, 'prefix')">{{ templateDataset[0].unit.prefix }}</span>
          <span :style="templateStyle(0, 'data')">{{ templateDataset[0].data.data1 }}</span>
          <span :style="templateStyle(0, 'backdrop')">{{ templateDataset[0].unit.backdrop }}</span>
        </span>
      </span>
    </span>
    <span v-else-if="selectedType === 'compare_card'" class="compare-card">
      <span v-for="(set, index) in templateDataset" :key="index" class="compare-card-body">
        <span class="compare-block">
          <span :style="justifyTitle">
            <span :style="templateStyle(index, 'title')">{{ set.title }}</span>
          </span>
          <span :style="justifyText">
            <span v-if="isTimeSubType" :style="templateStyle(index, 'data')">{{ set.data.data2 }}</span>
            <span :style="templateStyle(index, 'prefix')">{{ set.unit.prefix }}</span>
            <span :style="templateStyle(index, 'data')">{{ set.data.data1 }}</span>
            <span :style="templateStyle(index, 'backdrop')">{{ set.unit.backdrop }}</span>
          </span>
        </span>
        <span v-if="index === 0" :style="compareLine"></span>
      </span>
    </span>
    <span v-else-if="selectedType === 'description_card'">
      <span class="direction-card">
        <span v-for="(set, index) in templateDataset" :key="index" :style="alignContent">
          <span :style="templateStyle(index, 'prefix')"> {{ set.unit.prefix }}</span>
          <span :style="templateStyle(index, 'data')">{{ set.data.data1 }}</span>
        </span>
        <span :style="alignContent">
          <span :style="templateStyle(0, 'descriptionTitle')"> {{ templateDataset[0].description.title }} </span>
          <span :style="templateStyle(0, 'description')"> {{ templateDataset[0].description.content }} </span>
        </span>
      </span>
    </span>
    <span v-else class="text-card" :style="[textAlign, justifyContent]">
      <span :style="templateStyle(0, 'data')">{{ templateDataset[0].data.data1 }}</span>
    </span>
  </div>
  <Snackbar v-model="isShowSnackbar" :text="snackbarText"></Snackbar>
</template>

```

上面 Ml_textcard 在進入 PageView 進入 edit 的時候會被重新載入, 所以會 call base.ts 中的 getSnippetData:

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
            } else {
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
          console.log('get_dynamic_data in base.ts')
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
    console.log('getSnippetData in base.ts')
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
            console.log('get_dynamic_data in base.ts for informationData')
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

導致再次去 fetch 資料, 如果想避免進入 edit 的時候 fetch 資料的話, 應該怎麼做? (因為 PageView 通常會載入一堆不同的 Ml_ components, 所以無法從單一 Ml_ component 中去做修正)

🤖:[ChatClaude-3-5-Sonnet]
要避免進入編輯模式時重新 fetch 資料，我們需要從更高層的組件或狀態管理來控制。以下是一些可能的解決方案：

1. 使用狀態管理:

在 Pinia store 中添加一個標誌來表示是否正在進入編輯模式:

```typescript
// pageTreeStore.ts
export const usePageTreeStore = defineStore('pageTree', {
  state: () => ({
    // ... 其他狀態
    isEnteringEditMode: false,
  }),
  actions: {
    setEditMode(value: boolean) {
      this.isEditMode = value;
      this.isEnteringEditMode = value;
      // 設置一個短暫的延遲，然後將 isEnteringEditMode 設回 false
      setTimeout(() => {
        this.isEnteringEditMode = false;
      }, 100);
    }
  }
})
```

2. 修改 base.ts 中的 getSnippetData 函數:

```typescript
const getSnippetData = (setIndex: number | null, isAutoRefreshData: boolean = false) => {
  const pageTreeStore = usePageTreeStore();
  
  // 如果正在進入編輯模式，則不執行資料獲取
  if (pageTreeStore.isEnteringEditMode) {
    console.log('Entering edit mode, skipping data fetch');
    return Promise.resolve();
  }

  // ... 原有的邏輯
}
```

3. 在 SideBar.vue 中修改進入編輯模式的處理:

```vue
<template>
  <!-- ... -->
  <v-btn variant="text" icon @click="enterEditMode">
    <v-icon icon="mdi-pencil"></v-icon>
    <v-tooltip activator="parent" location="bottom">{{ t('sidebar.edit') }}</v-tooltip>
  </v-btn>
  <!-- ... -->
</template>

<script setup>
// ... 其他 imports
import { usePageTreeStore } from "@/stores/pageTree";

const pageTreeStore = usePageTreeStore();

const enterEditMode = () => {
  pageTreeStore.setEditMode(true);
}
</script>
```

這種方法的原理是：

1. 當用戶點擊編輯按鈕時，我們設置一個短暫的 "正在進入編輯模式" 的狀態。
2. 在這個短暫的時間窗口內，所有的資料獲取請求都會被跳過。
3. 這個時間窗口過後，正常的資料獲取邏輯會恢復，但由於已經進入了編輯模式，大多數元件應該不會觸發新的資料獲取。

這種方法的優點是它不需要修改每個單獨的 Ml_ component，而是在更高的層次上控制資料獲取行為。缺點是它引入了一個短暫的時間窗口，在這個窗口內可能會略微影響用戶體驗。但這個時間窗口可以設置得非常短，以至於用戶幾乎不會注意到。

需要注意的是，這種方法假設進入編輯模式後，大多數元件不應該獲取新數據。如果有些元件在編輯模式下仍然需要獲取新數據，那麼可能需要為這些元件添加特殊的處理邏輯。
