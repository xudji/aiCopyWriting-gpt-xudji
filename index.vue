<!-- <template>
  <div>
    <iframe src="https://chatgpt-discount.zeabur.app/" class="iframe"></iframe>
  </div>
</template>
<script setup lang="ts">
</script>
<style lang="scss" scoped>
.iframe {
  width: 100%;
  height: calc(100vh - 165px);
  border: inherit;
}
</style> -->
<template>
  <div class="aiContent">
    <a-row>
      <a-col :span="6" class="Aileft">
        <div class="addNewChat">
          <span class="aiSpanNoBlue aiSpanActiveBG" @click="addNewList">
            新建对话
          </span>
        </div>
        <div class="leftList" v-if="hisList.length > 0">
          <div class="leftTitle">历史对话</div>
          <div class="leftListChat" @mouseenter="handleMouseEnter(index)" @mouseleave="handleMouseLeave"
            v-for="(item, index) in hisList" :key="item.id">
            <div class="leftListChatItem aiSpanNoBlueS" :class="chatId == item.id ? 'aiSpanActive' : ''"
              @click.stop="selectHis(item, index)">
              <div class="ellipsis-title" style="width: 205px;" v-if="!(isshwoInputId == index)"> {{
        item.chatName }}</div>
           <a-input :maxlength="20" @blur="editSave(item)" @pressEnter="editSave(item)" @click.stop=""
                v-model:value="item.chatName" placeholder="新建对话" v-if="isshwoInputId == index" />
              <div class="iconList" v-if="hoveredId == index">
                <a-tooltip placement="bottomLeft">
                  <template #title>
                    <span>重命名</span>
                  </template>
                  <img @click.stop="editT(item, index)" class="leftIcon"
                    src="/src/assets/images/imgs/addTypeEdit.png"></img>
                </a-tooltip>
                <a-tooltip placement="bottomLeft">
                  <template #title>
                    <span>删除</span>
                  </template>
                  <img class="leftIcon" @click.stop="delOpen(item, index)"
                    src="/src/assets/images/imgs/addTypeEdit2.png"></img>
                </a-tooltip>
              </div>
            </div>
          </div>
        </div>
        <div v-else class="noHistory">
          <img style="width:88px;height: 88px;" src="../../assets/images/imgs/nohis111.svg" alt="">
          <span>暂无历史对话</span>
        </div>
      </a-col>
      <a-col :span="18" class="AiRight">
        <div id="chatContent" class="chatContent">
          <div class="aiAssistant" v-if="!hasNoChat">
            <div class="aiNav">
              <div class="aiTitle">AI助手</div>
              <div class="aiChange">
                <img src="../../assets/images/imgs/111left.svg" style="font-weight: bold;margin-right: 10px;"
                  @click="prevPage"></img>
                <span>换一页</span>
                <img src="../../assets/images/imgs/222right.svg" style="font-weight: bold;margin-left: 10px;"
                  @click="nextPage"></img>
              </div>
            </div>
            <!-- 场景 -->
            <div class="aiCardContent">
              <div class="aiCard" v-for="item in aiCardContent" :key="item.id" @click="sendTextArea(item.id, item)">
                <div class="aiCardLeftBg">
                  <img style="width: 16px;" class="aiCardLeft" src="../../assets/images/imgs/area.svg" alt="">
                </div>
                <div class="aiCardRight">
                  <div class="aiCardRightTitle">{{ item.sceneName }}</div>
                  <div class="aiCardRightPrompt">{{ item.sceneDetail }}</div>
                </div>
              </div>
            </div>
          </div>
          <div class="chatDialog" style="padding-top: 10px;" v-if="hasNoChat" v-for="(item, index) in chatList"
            :key="item.id">
            <div class="chatUser marginBottom" v-if="item.role == 'system' || item.role == 'user'">
              <img class="chatUserLeft" src="../../assets/images/imgs/aver.png" alt="">
              <div class="chatUserRight paddingEx">
                {{ item.content }}
              </div>
            </div>
            <div class="chatAssistant marginBottom" v-if="item.role == 'assistant'">
              <img style="margin-left: -8px;margin-right: -5px;width: 56px;
    height: 45px;" class="assistantLeft" src="../../assets/images/imgs/bot.png" alt="">
              <div class="assistantRight paddingEx">
                <div class="assLoading" v-if="showLoading && item.num == reflag">
                  <img src="../../assets/images/custom/loading.gif" alt="">
                  <span>AI助手正在整理答案</span>
                </div>



                <div v-else>

                  <div class="assContent">
                    {{ item.content }}
                  </div>

                  <div v-if="sensitive"> </div>
                  <div v-else>
                    <div v-if="item.role == 'assistant' && item.num == reflag && closeFlag ||  showLoading && item.num == reflag" @click="stopTexts">
                      <div class="assistantStop">
                        <img style="width: 8px;height: 8px;" class="stopIcon" src="../../assets/images/imgs/stoppp.svg"
                          alt="">
                        <div class="stopText">停止生成</div>
                      </div>
                    </div>
                    <div v-else class="assistantEdit">
                      <a-tooltip placement="bottomLeft">
                        <template #title>
                          <span>重新生成</span>
                        </template>
                        <img style="width: 16px;height: 16px; " @click="reCreatingChat(item)"
                          src="../../assets/images/imgs/resend.svg" />
                      </a-tooltip>
                      <a-tooltip placement="bottomLeft">
                        <template #title>
                          <span>复制文本</span>
                        </template>
                        <img style="width: 16px; height: 16px;" @click="copyText(item.content)" class="imgLeft"
                          src="../../assets/images/imgs/copy.svg" />
                      </a-tooltip>
                      <a-tooltip placement="bottomLeft">
                        <template #title>
                          <span>制作视频</span>
                        </template>
                        <img style="width: 16px;height: 16px; " @click="createVoid(item.content)" class="imgLeft"
                          src="../../assets/images/imgs/videos.svg" />
                      </a-tooltip>
                    </div>

                  </div>

                </div>


              </div>
            </div>
          </div>
        </div>
        <div class="chatFooter">
          <div class="chatInput" v-if="!sensitive">
            <a-textarea :auto-size="{ minRows: 3, maxRows: 3 }" placeholder="在这里输入你想提问的内容，按CTRL+Enter快捷发送"
              :bordered="false" @keydown.ctrl.enter.native="sendText()" class="chatTextarea"
              v-model:value="sendValue" />
            <div class="sendBtn" @click="sendText">发送</div>
            <!-- <div class="sendBtn" @click="stopTexts">停止</div> -->
          </div>
          <div class="sensitive" v-else @click="addNewList">
            点击这里换个话题
          </div>
          <div class="chatTip">所有内容均由人工智能模型输出，其内容的准确性和完整性无法保证，不代表我们的态度或观点</div>
        </div>
      </a-col>
    </a-row>

    <el-dialog v-model="delModalFlag" title="删除对话" width="582" :before-close="closeDelModal"
      style="border-radius: 18px;">
      <template #header="{ titleId, titleClass }">
        <div class="my-header">
          <div :id="titleId" class="titleClass">删除对话</div>
        </div>
      </template>

      <div style="height: 150px;margin-top: 40px;font-size: 16px; color: #262626;margin-left: 20px;">你是否确定删除该对话?</div>
      <div class="del-footer">
        <div @click="closeDelModal" class="cancal">取消</div>
        <div @click="delT" class="confirm">
          确定
        </div>
      </div>
    </el-dialog>
  </div>
</template>
<script setup lang="ts">
import request from '@/utils/request'
import { useRouter } from 'vue-router';
import { ElMessage } from "element-plus";
import { fetchEventSource } from '@microsoft/fetch-event-source';
import { useUserStore } from '@/stores/user'
import { Modal } from 'ant-design-vue';
import {
  throttle
} from "@/utils/index";

import {
  ref,
  onBeforeMount,
  onMounted,
  onBeforeUnmount,
  onUnmounted,
} from "vue";
import { ChatLineRound } from '@element-plus/icons-vue/dist/types';
const router = useRouter();
let hasList = ref(true);
// 右侧对话
let chatList = ref([])
// 左侧历史
let closeFlag = ref(false)
let hisList = ref([])
let selectId = ref(0)
let showEditId = ref(null)
let isshwoInputId = ref(null) // 展示input
let hoveredId = ref(null)
let sendValue = ref('')
let Chat = ref(false) // 正在生成
let showLoading = ref(false)
let sensitiveFlag = ref(true)
let delModalFlag = ref(false)


const truncatedChatName = (item) => {
  return item.slice(0,20)
}

let sse = ref()
const getUrl = () => {
  request({
    method: 'GET',
    url: '/biz/commonInfo/list',
  }).then((res: any) => {
    res.rows.forEach((v: any) => {
      if (v.name == 'AI_ASSISTANT_CHAT_SSE_URL') {
        sse.value = v.value
        console.log('sse.value', sse.value)
      }
    });
  })
}











let aiCardContent = ref([
  {
    id: 0,
    icon: '/src/assets/images/imgs/aiImg.png',
    title: '直播剧本大师',
    prompt: '我可以帮你思考直播剧本'
  }, {
    id: 1,
    icon: '/src/assets/images/imgs/aiImg.png',
    title: '短视频创作达人',
    prompt: '我有很多短视频创作灵感'
  }, {
    icon: '/src/assets/images/imgs/aiImg.png',
    title: '新闻记者',
    prompt: '输入新闻时间，帮你整理稿件'
  }, {
    id: 2,
    icon: '/src/assets/images/imgs/aiImg.png',
    title: '朋友圈文案',
    prompt: '帮你成为朋友圈最闪亮的仔'
  }, {
    id: 3,
    icon: '/src/assets/images/imgs/aiImg.png',
    title: '法律咨询',
    prompt: '提供你的困难，给你法律建议'
  }, {
    id: 4,
    icon: '/src/assets/images/imgs/aiImg.png',
    title: '翻译专家',
    prompt: '专业的多国语言翻译'
  },
])


onMounted(() => {
  getUrl()
  // 获取历史列表
  getAiAssistantChatList()
  // 获取ai场景
  getAiAssistantChatScene(pageNum.value, pageSize.value)

  // window.onbeforeunload = function(event) {
  //   if (closeFlag.value) {
  //     const message = "对话正在进行中，请不要刷新网页";
  //     if (event) {
  //       event.returnValue = message; 
  //     }
  //     return message; 
  //   }
  // };

})

// 计算有无对话
const hasNoChat = computed(() => {
  if (chatList.value.length > 0) {
    return true
  } else {
    return false
  }
});

const computeCreate = (item) => {
  console.log(item)
  let find = chatList.value.find((e) => e.num == item.num)
  return find.num
}


let chatId = ref()

// 新建对话
const throttleaddNewList = () => {
  if (closeFlag.value) {
    return false
  } else {
    aiAssistantChatgetInfo()
    addAiAssistantChat()
  }
}
const addNewList = throttle(throttleaddNewList)

const addAiAssistantChatTemp = () => {
  request({
    method: "post",
    url: `/biz/aiAssistantChat/add`,
    data: {
    },
  }).then((res: any) => {
    console.log('添加会话', res)
    getAiAssistantChatListTemp()
  });
}
// const addNewListTemp = () => {
//   addAiAssistantChatTemp()
// }
const addNewListTemp = throttle(addAiAssistantChatTemp)

// 判断发送中

const sendFlag = (item) => {
  console.log('sendfal', item)
}

// 选择对话 切换
const throttleselectHis = (item, index) => {
  if (closeFlag.value) {
    return false
  } else {

    console.log('选择对话', item, index)
    sendValue.value = ''
    // selectId.value = index
    // chatId.value = item.id
    isshwoInputId.value = null
    showEditId.value = null
    // 查对应的右边对话

    const chatContent = document.getElementById('chatContent');
    chatContent.scrollTop = chatContent.scrollHeight;
    chatId.value = item.id


    aiAssistantChatgetInfo()
    getAiAssistantChatContent()
  }

}
const selectHis = throttle(throttleselectHis, 200)




const handleMouseEnter = (index) => {
  hoveredId.value = index;
  // isshwoInputId.value = index
}
const handleMouseLeave = () => {
  hoveredId.value = null;
  isshwoInputId.value = null
}
const editT = (item, index) => {
  console.log(111,closeFlag.value)
  if(closeFlag.value){
    console.log(11111)
  } else {
    isshwoInputId.value = index
  chatId.value = item.id
  }
  
}
let delIds = ref([])
const delOpen = (item, index) => {
  
  if(closeFlag.value){
    console.log('closeFlag.value',closeFlag.value)
    return false
  } else {
    chatId.value = item.id
  delModalFlag.value = true
  delIds.value = [item.id]
  }

  
}
const delT = () => {
  delModalFlag.value = false
  removeAiAssistantChat()
}

const closeDelModal = () => {
  delModalFlag.value = false
}

// 失去焦点 或者enter保存
const editSave = (item) => {

  if(closeFlag.value){
    return false
  } else {
    isshwoInputId.value = null;
  editAiAssistantChat(item)
  }
 
    
  
 

  
}

let subscription = ref()


const throttlestopTexts = () => {
  controller.value.abort()
  // subscription.unsubscribe()
  sseText.value = ''
  closeFlag.value = false
  sceneId.value = null
}
const stopTexts = throttle(throttlestopTexts, 2000)





let sceneId = ref(null)
// 场景生成 发送场景
const sendTextArea = (id, item) => {
  console.log('提示场景', sceneId, item)
  sceneId.value = id
  // console.log('sceneId', sceneId)

  if (hisList.value.length <= 0) {
    // 先新建对话 然后生成
    console.log('发送场景', item)
    addAiAssistantChats(item)
    return false

  } else {
    const matchingChatIndex = hisList.value.findIndex((chat) => chat.id == chatId.value);

    if (matchingChatIndex !== -1) {
      if (hisList.value[matchingChatIndex].chatName == '新的对话') {
        hisList.value[matchingChatIndex].chatName = item.sceneName;
        editAiAssistantChats(item.sceneName)
      }

    } else {
      console.warn(`未找到 id 为 ${chatId.value} 的聊天记录`);
    }


    const lastItem = chatList.value[chatList.value.length - 1];
    const newItemUser = {
      createdTime: new Date().toISOString(),
      createdBy: "system",
      updatedTime: new Date().toISOString(),
      updatedBy: "system",
      delFlag: 0,
      id: lastItem ? lastItem.id + 1 : 1,
      chatId: lastItem ? lastItem.chatId : 1,
      role: "user",
      num: lastItem ? lastItem.num + 1 : 0,
      content: item.sceneContent,
    }
    chatList.value.push(newItemUser);
    const newItemAssistant = {
      createdTime: new Date().toISOString(),
      createdBy: "system",
      updatedTime: new Date().toISOString(),
      updatedBy: "system",
      delFlag: 0,
      id: lastItem ? lastItem.id + 2 : 2,
      chatId: lastItem ? lastItem.chatId : 2,
      role: "assistant",
      num: lastItem ? lastItem.num + 2 : 1,
      content: sseText.value,
    };

    reflag.value = newItemAssistant.num
    chatList.value.push(newItemAssistant);

    console.log('chatList.value', chatList.value)
    closeFlag.value = true

    subscription.value = fetchAndSubscribeToChat(
      chatId.value, null, null, sceneId.value

    );

  }






}










const editAiAssistantChats = (item) => {
  let id = chatId.value
  let chatName = item
  request({
    method: "post",
    url: `/biz/aiAssistantChat/edit`,
    data: {
      id,
      chatName
    },
  }).then((res: any) => {
    console.log('修改会话', res)
    // getAiAssistantChatList()
  });
}

// 查敏感信息

const aiAssistantChatgetInfo = () => {
  let id = chatId.value
  request({
    method: "post",
    url: `/biz/aiAssistantChat/getInfo`,
    data: {
      id,
    },
  }).then((res: any) => {
    console.log('敏感信息', res.data.chatStatus)
    if (res.data.chatStatus == 'BLOCK') {
      sensitive.value = true
    } else {
      sensitive.value = false
    }
  });
}



const sendText = () => {

  // 左边没有历史对话 先新建一个 全走完接口 再调用 senText



  console.log('发送对话', sendValue.value)
  console.log('chatList.valuechatList.value', chatList.value)
  console.log('hisList.value', hisList.value)

  // if( hisList.value.length > 0 && hisList.value[chatId.value].chatName == '新建对话') {
  //   hisList.value[chatId.value].chatName = sendValue.value
  // }


  if (!sendValue.value || closeFlag.value) {
    console.log('不让发送')
    return false
  } else {

    if (hisList.value.length <= 0) {
      addNewListTemp()
      return false
    }


    const matchingChatIndex = hisList.value.findIndex((chat) => chat.id == chatId.value);

    if (matchingChatIndex !== -1) {
      if (hisList.value[matchingChatIndex].chatName == '新的对话') {
        hisList.value[matchingChatIndex].chatName = sendValue.value.slice(0,20);
        editAiAssistantChats(sendValue.value.slice(0,20))
      }

    } else {
      console.warn(`未找到 id 为 ${chatId.value} 的聊天记录`);
    }
    // 判断右边没数据就好了


    // 请求讯飞
    const lastItem = chatList.value[chatList.value.length - 1];
    const newItemUser = {
      createdTime: new Date().toISOString(),
      createdBy: "system",
      updatedTime: new Date().toISOString(),
      updatedBy: "system",
      delFlag: 0,
      id: lastItem ? lastItem.id + 1 : 1,
      chatId: lastItem ? lastItem.chatId : 1,
      role: "user",
      num: lastItem ? lastItem.num + 1 : 0,
      content: sendValue.value,
    }
    chatList.value.push(newItemUser);
    const newItemAssistant = {
      createdTime: new Date().toISOString(),
      createdBy: "system",
      updatedTime: new Date().toISOString(),
      updatedBy: "system",
      delFlag: 0,
      id: lastItem ? lastItem.id + 2 : 2,
      chatId: lastItem ? lastItem.chatId : 2,
      role: "assistant",
      num: lastItem ? lastItem.num + 2 : 1,
      content: sseText.value,
    };
    reflag.value = newItemAssistant.num
    chatList.value.push(newItemAssistant);

    console.log('reflag.value', reflag.value)
    console.log('chatList.value', chatList.value)
    const truncatedValue = sendValue.value.slice(0, 1000);
    subscription.value = fetchAndSubscribeToChat(
      chatId.value, truncatedValue, null, null
    );
  }

}

let sensitive = ref(false)
let controller = ref()
let sseText = ref('')
// 连接sse连接
function fetchAndSubscribeToChat(chatId = null, sendValues = null, num = null, sceneIds = null) {
  let userStoreInfo = useUserStore();
  let Authorizations = userStoreInfo.token || '';

  controller.value = new AbortController();
  const signal = controller.value.signal;


  let timerId;

  const timerCallback = () => {
    showLoading.value = true;
  };

  timerId = setTimeout(timerCallback, 3000);

  fetchEventSource(sse.value, {
    method: 'POST',
    signal: signal,
    headers: {
      "Content-Type": 'application/json',
      Authorization: Authorizations
    },
    body: JSON.stringify(
      {
        chatId: chatId,
        content: sendValues,
        num: num,
        sceneId: sceneIds
      }
    ),
    onopen() {
      const chatContent = document.getElementById('chatContent');
      chatContent.scrollTop = chatContent.scrollHeight - chatContent.clientHeight;
      sendValue.value = ''
      console.log(11111111111111111)
      // getAiAssistantChatContent()
    },
    onmessage(msg) {
      /* 
      data:{"msg":"操作成功","code":200,"data":{"createdTime":null,"createdBy":null,"updatedTime":null,"updatedBy":null,"delFlag":null,"id":null,"chatId":null,"role":"assistant","num":null,"content":"主题"}}
data:{"msg":"操作成功","code":200,"data":{"createdTime":null,"createdBy":null,"updatedTime":null,"updatedBy":null,"delFlag":null,"id":null,"chatId":null,"role":"assistant","num":null,"content":"：揭秘"}}
      */
      closeFlag.value = true
      console.log('reflag.value ', reflag.value)
      clearTimeout(timerId);  // 清除计时器

      showLoading.value = false;
      let jsonData = JSON.parse(msg.data).data;
      console.log('jsonData', jsonData)
      // 检查 role 是否为 "assistant"
      if (jsonData.role == 'assistant') {
        // 如果是，将新消息的 content 添加到累积文本中
        sseText.value += jsonData.content;
      }
      console.log('tempData', sseText.value);
      chatList.value[chatList.value.length - 1].content = sseText.value

      // const chatContent = document.getElementById('chatContent');
      // chatContent.scrollTop = chatContent.scrollHeight - chatContent.clientHeight;
      // smoothScrollToBottom(500);
      smoothScrollToBottoms()
    },
    onerror(err) {
      clearTimeout(timerId);  // 清除计时器
      console.log(err)
    },
    openWhenHidden:true,
    onclose() {
      // const chatContent = document.getElementById('chatContent');
      // chatContent.scrollTop = chatContent.scrollHeight - chatContent.clientHeight;
      // smoothScrollToBottom(500);
      smoothScrollToBottoms()
      console.log('关闭了sse')
      // 查敏感信息
      aiAssistantChatgetInfo()
      reflag.value = null
      // getAiAssistantChatContent()
      sseText.value = ''
      closeFlag.value = false
      sceneId.value = null
      // controller.value.abort();
    },
  });
}


function scrollToBottom() {
  const chatContent = document.getElementById('chatContent');
  chatContent.scrollTop = chatContent.scrollHeight - chatContent.clientHeight;
}

function smoothScrollToBottoms() {
  requestAnimationFrame(scrollToBottom);
}

function smoothScrollToBottom(duration = 500) {
  const chatContent = document.getElementById('chatContent');
  const start = chatContent.scrollTop;
  const end = chatContent.scrollHeight - chatContent.clientHeight;
  const step = Math.max(10, Math.ceil(duration / 75)); // 每隔约75ms更新一次，最小间隔为10ms
  const increment = (end - start) / step;

  let current = start;
  const timer = setInterval(() => {
    current += increment;
    if (current >= end) {
      clearInterval(timer);
      chatContent.scrollTop = end;
    } else {
      chatContent.scrollTop = current;
    }
  }, 75);
}
let reflag = ref()

// 重新生成
window.onbeforeunload = function () {

  //dosomething...
  alert('刷新网页')

}

// const reCreatingChat = (item) => {
//   console.log('点击重新生成', item.num)
//   console.log('对话列表', chatList.value.length - 1)
//   reflag.value = item.num
//   console.log('reflag.value', reflag.value)
//   closeFlag.value = true
//   subscription.value = fetchAndSubscribeToChatRe(
//     chatId.value,
//     null,
//     item.num,
//     null
//   );
// }
const throttlereCreatingChat = (item) => {
  console.log('点击重新生成', item.num)
  console.log('对话列表', chatList.value.length - 1)
  reflag.value = item.num
  console.log('reflag.value', reflag.value)
  closeFlag.value = true
  subscription.value = fetchAndSubscribeToChatRe(
    chatId.value,
    null,
    item.num,
    null
  );
}
const reCreatingChat = throttle(throttlereCreatingChat, 2000)





function fetchAndSubscribeToChatRe(chatId = null, sendValues = null, num = null, sceneId = null) {
  let userStoreInfo = useUserStore();
  let Authorizations = userStoreInfo.token || '';

  controller.value = new AbortController();
  const signal = controller.value.signal;
  let timerId;

  const timerCallback = () => {
    showLoading.value = true;
  };

  timerId = setTimeout(timerCallback, 3000);

  fetchEventSource(sse.value, {
    method: 'POST',
    signal: signal,
    headers: {
      "Content-Type": 'application/json',
      Authorization: Authorizations
    },
    body: JSON.stringify(
      {
        chatId: chatId,
        content: sendValues,
        num: num,
        sceneId: sceneId
      }
    ),
    onopen() {

      sendValue.value = ''
      console.log(11111111111111111)
      // getAiAssistantChatContent()
    },
    onmessage(msg) {

      clearTimeout(timerId);  // 清除计时器

      showLoading.value = false;
      let jsonData = JSON.parse(msg.data).data;
      console.log('jsonData', jsonData)
      // 检查 role 是否为 "assistant"
      if (jsonData.role == 'assistant') {
        // 如果是，将新消息的 content 添加到累积文本中
        sseText.value += jsonData.content;
      }
      console.log('tempData', sseText.value);
      chatList.value[num].content = sseText.value
    },
    onerror(err) {
      clearTimeout(timerId);  // 清除计时器
      console.log(err)
    },
    openWhenHidden:true,
    onclose() {
      reflag.value = null
      // getAiAssistantChatContent()
      sseText.value = ''
      closeFlag.value = false
      // controller.value.abort();
    },
  });
}










// 复制文本
const copyText = (text: string) => {
  return new Promise((resolve, reject) => {
    try {
      const input: HTMLTextAreaElement = document.createElement('textarea')
      input.setAttribute('readonly', 'readonly')
      input.value = text
      document.body.appendChild(input)
      input.select()
      if (document.execCommand('copy'))
        document.execCommand('copy')
      document.body.removeChild(input)
      ElMessage({
        message: '文本已复制成功',
        type: 'success',
      })
      resolve(text)
    }
    catch (error) {
      reject(error)
    }
  })
}

// 制作视频
const createVoid = (text: string) => {
  router.push({
    path: '/make',
    query: {
      text: text
    }
  })
}

const stopCreate = () => {
  console.log('停止生成')

}

// 翻页场景
let pageNum = ref(1);
let pageSize = ref(6)
let total = ref(10);

// 左翻页函数
const prevPage = () => {
  const totalPages = Math.ceil(total.value / pageSize.value);
  if (pageNum.value === 1) {
    // 到达第一页时，跳转到最后一页
    pageNum.value = totalPages;
    getAiAssistantChatScene(pageNum.value, pageSize.value);

  } else {
    pageNum.value--;
    getAiAssistantChatScene(pageNum.value, pageSize.value);
  }
};

// 右翻页函数
const nextPage = () => {
  const totalPages = Math.ceil(total.value / pageSize.value);
  if (pageNum.value === totalPages) {
    // 到达最后一页时，跳转到第一页
    pageNum.value = 1;
    getAiAssistantChatScene(pageNum.value, pageSize.value);

  } else {
    pageNum.value++;
    getAiAssistantChatScene(pageNum.value, pageSize.value);
  }
};


const getAiAssistantChatLists = (item) => {
  request({
    method: "post",
    url: `/biz/aiAssistantChat/list?pageNum=1&pageSize=500`,
    data: {
    },
  }).then((res: any) => {
    console.log('会话列表', res.rows)
    if (res.rows.length > 0) {
      let sortedList = res.rows.slice().sort((a, b) => new Date(b.updatedTime).getTime() - new Date(a.updatedTime).getTime())
      hisList.value = sortedList
      chatId.value = hisList.value[0].id

      const matchingChatIndex = hisList.value.findIndex((chat) => chat.id == chatId.value);

      if (matchingChatIndex !== -1) {
        if (hisList.value[matchingChatIndex].chatName == '新的对话') {
          hisList.value[matchingChatIndex].chatName = item.sceneName;
          editAiAssistantChats(item.sceneName)
        }

      } else {
        console.warn(`未找到 id 为 ${chatId.value} 的聊天记录`);
      }

      // reflag.value = 
      const lastItem = chatList.value[chatList.value.length - 1];
      const newItemUser = {
        createdTime: new Date().toISOString(),
        createdBy: "system",
        updatedTime: new Date().toISOString(),
        updatedBy: "system",
        delFlag: 0,
        id: lastItem ? lastItem.id + 1 : 1,
        chatId: lastItem ? lastItem.chatId : 1,
        role: "user",
        num: lastItem ? lastItem.num + 1 : 0,
        content: item.sceneContent,
      }
      chatList.value.push(newItemUser);
      const newItemAssistant = {
        createdTime: new Date().toISOString(),
        createdBy: "system",
        updatedTime: new Date().toISOString(),
        updatedBy: "system",
        delFlag: 0,
        id: lastItem ? lastItem.id + 2 : 2,
        chatId: lastItem ? lastItem.chatId : 2,
        role: "assistant",
        num: lastItem ? lastItem.num + 2 : 1,
        content: sseText.value,
      };
      reflag.value = newItemAssistant.num
      chatList.value.push(newItemAssistant);

      console.log('chatList.value', chatList.value)
      closeFlag.value = true

      subscription.value = fetchAndSubscribeToChat(
        chatId.value, null, null, sceneId.value

      );
      // getAiAssistantChatContent()
    } else {
      console.log('没有历史对话')
      hisList.value = res.rows
      chatList.value = res.rows
    }

  });
};


// 查询ai助手会话列表 历史列表
const getAiAssistantChatList = () => {
  request({
    method: "post",
    url: `/biz/aiAssistantChat/list?pageNum=1&pageSize=500`,
    data: {
    },
  }).then((res: any) => {
    console.log('会话列表', res.rows)
    if (res.rows.length > 0) {
      let sortedList = res.rows.slice().sort((a, b) => new Date(b.updatedTime).getTime() - new Date(a.updatedTime).getTime())
      hisList.value = sortedList
      chatId.value = hisList.value[0].id
      aiAssistantChatgetInfo()
      getAiAssistantChatContent()
    } else {
      console.log('没有历史对话')
      hisList.value = res.rows
      chatList.value = res.rows
    }

    /* 
    {
    "total": 2,
    "rows": [
        {
            "createdTime": "2024-04-12T16:27:34.000+08:00",
            "createdBy": "358",
            "updatedTime": "2024-04-12T16:27:35.000+08:00",
            "updatedBy": "358",
            "delFlag": 0,
            "id": 426,
            "chatName": "直播剧本大师",
            "chatStatus": "NORMAL",
            "userId": 358
        },
        {
            "createdTime": "2024-04-12T16:28:02.000+08:00",
            "createdBy": "358",
            "updatedTime": "2024-04-12T16:33:56.000+08:00",
            "updatedBy": "358",
            "delFlag": 0,
            "id": 427,
            "chatName": "新闻记者",
            "chatStatus": "NORMAL",
            "userId": 358
        }
    ],
    "code": 200,
    "msg": "查询成功"
}
    */

  });
};


// 查询ai助手会话列表 历史列表
const getAiAssistantChatListTemp = () => {
  request({
    method: "post",
    url: `/biz/aiAssistantChat/list?pageNum=1&pageSize=500`,
    data: {
    },
  }).then((res: any) => {
    console.log('会话列表', res.rows)
    if (res.rows.length > 0) {
      let sortedList = res.rows.slice().sort((a, b) => new Date(b.updatedTime).getTime() - new Date(a.updatedTime).getTime())
      hisList.value = sortedList
      chatId.value = hisList.value[0].id
      // getAiAssistantChatContent()


      const matchingChatIndex = hisList.value.findIndex((chat) => chat.id == chatId.value);

      if (matchingChatIndex !== -1) {
        if (hisList.value[matchingChatIndex].chatName == '新的对话') {
          hisList.value[matchingChatIndex].chatName = sendValue.value.slice(0,20);
        editAiAssistantChats(sendValue.value.slice(0,20))
        }

      } else {
        console.warn(`未找到 id 为 ${chatId.value} 的聊天记录`);
      }
      // 判断右边没数据就好了


      // 请求讯飞
      const lastItem = chatList.value[chatList.value.length - 1];
      const newItemUser = {
        createdTime: new Date().toISOString(),
        createdBy: "system",
        updatedTime: new Date().toISOString(),
        updatedBy: "system",
        delFlag: 0,
        id: lastItem ? lastItem.id + 1 : 1,
        chatId: lastItem ? lastItem.chatId : 1,
        role: "user",
        num: lastItem ? lastItem.num + 1 : 0,
        content: sendValue.value,
      }
      chatList.value.push(newItemUser);
      const newItemAssistant = {
        createdTime: new Date().toISOString(),
        createdBy: "system",
        updatedTime: new Date().toISOString(),
        updatedBy: "system",
        delFlag: 0,
        id: lastItem ? lastItem.id + 2 : 2,
        chatId: lastItem ? lastItem.chatId : 2,
        role: "assistant",
        num: lastItem ? lastItem.num + 2 : 1,
        content: sseText.value,
      };
      reflag.value = newItemAssistant.num
      chatList.value.push(newItemAssistant);

      console.log('reflag.value', reflag.value)
      console.log('chatList.value', chatList.value)
      const truncatedValue = sendValue.value.slice(0, 1000);
      subscription.value = fetchAndSubscribeToChat(
        chatId.value, truncatedValue, null, null
      );




    } else {
      console.log('没有历史对话')
      hisList.value = res.rows
      chatList.value = res.rows
    }

  });
};

// 新建对话
const addAiAssistantChats = (item) => {
  request({
    method: "post",
    url: `/biz/aiAssistantChat/add`,
    data: {
    },
  }).then((res: any) => {
    console.log('添加会话', res)
    getAiAssistantChatLists(item)
    /* 
    {
    "msg": "操作成功",
    "code": 200,
    "data": {
        "createdTime": "2024-04-12T16:32:34.153+08:00",
        "createdBy": "358",
        "updatedTime": "2024-04-12T16:32:34.153+08:00",
        "updatedBy": "358",
        "delFlag": 0,
        "id": 429,
        "chatName": "新的对话",
        "chatStatus": "NORMAL",
        "userId": 358
    }
}
    */
  });
}


const addAiAssistantChat = () => {
  request({
    method: "post",
    url: `/biz/aiAssistantChat/add`,
    data: {
    },
  }).then((res: any) => {
    console.log('添加会话', res)
    getAiAssistantChatList()
  });
}




// 编辑ai会话  
const editAiAssistantChat = (item: any) => {
  let id = item.id
  let chatName = item.chatName
  request({
    method: "post",
    url: `/biz/aiAssistantChat/edit`,
    data: {
      id,
      chatName
    },
  }).then((res: any) => {
    console.log('修改会话', res)
    /* 
    {
    "msg": "操作成功",
    "code": 200,
    "data": {
        "createdTime": null,
        "createdBy": null,
        "updatedTime": "2024-04-12T16:33:56.446+08:00",
        "updatedBy": "358",
        "delFlag": null,
        "id": 427,
        "chatName": "新闻记者",
        "chatStatus": null,
        "userId": null
    }
}
    */
    getAiAssistantChatList()
  });
}
// 删除会话  
const removeAiAssistantChat = () => {
  let ids = delIds.value
  request({
    method: "post",
    url: `/biz/aiAssistantChat/remove`,
    data: {
      ids: ids
    },
  }).then((res: any) => {
    console.log('删除会话', res)
    /* 
    {"msg":"操作成功","code":200}
    */
    getAiAssistantChatList()
  });
}

// ai场景
const getAiAssistantChatScene = (pageNum: number, pageSize: number) => {
  request({
    method: "post",
    url: `/biz/aiAssistantChatScene/list?pageNum=${pageNum}&pageSize=${pageSize}`,
    data: {
    },
  }).then((res: any) => {
    console.log('ai场景', res)
    aiCardContent.value = res.rows
    total.value = res.total
    /* 
    {
    "total": 7,
    "rows": [
        {
            "createdTime": "2024-03-19T11:15:02.000+08:00",
            "createdBy": "system",
            "updatedTime": "2024-03-19T11:15:02.000+08:00",
            "updatedBy": "system",
            "delFlag": 0,
            "id": 1,
            "sceneName": "直播剧本大师",
            "sceneDetail": "我可以帮你思考直播剧本",
            "sceneContent": "你现在是直播剧本大师，我给你一个主题，你要根据主题输出一份用于直播的剧本"
        },
        {
            "createdTime": "2024-03-19T11:15:02.000+08:00",
            "createdBy": "system",
            "updatedTime": "2024-03-19T11:15:02.000+08:00",
            "updatedBy": "system",
            "delFlag": 0,
            "id": 2,
            "sceneName": "朋友圈文",
            "sceneDetail": "帮你成为朋友圈最闪亮的仔",
            "sceneContent": "你现在是一名朋友圈内容运营，擅长写分享文案，我给你一个主题，你根据主题输出一段吸引人的文案"
        },
        {
            "createdTime": "2024-03-19T11:15:02.000+08:00",
            "createdBy": "system",
            "updatedTime": "2024-03-19T11:15:02.000+08:00",
            "updatedBy": "system",
            "delFlag": 0,
            "id": 3,
            "sceneName": "短视频创作达人",
            "sceneDetail": "我有很多短视频创作灵感",
            "sceneContent": "你现在是一名短视频创作达人，我i给你一个主题，你根据主题输出一份用于制作短视频得剧本"
        },
        {
            "createdTime": "2024-03-19T11:15:02.000+08:00",
            "createdBy": "system",
            "updatedTime": "2024-03-19T11:15:02.000+08:00",
            "updatedBy": "system",
            "delFlag": 0,
            "id": 4,
            "sceneName": "法律咨询",
            "sceneDetail": "提供你的困难，给你法律建议",
            "sceneContent": "你现在是一名法律顾问，要为我提供专业的法律建议，要引用相关得法律条文"
        },
        {
            "createdTime": "2024-03-19T11:15:02.000+08:00",
            "createdBy": "system",
            "updatedTime": "2024-03-19T11:15:02.000+08:00",
            "updatedBy": "system",
            "delFlag": 0,
            "id": 5,
            "sceneName": "新闻记者",
            "sceneDetail": "输入新闻时间，帮你整理稿件",
            "sceneContent": "你现在是一名中新闻记者，我给你内容，你要根据内容总结成一份新闻简报"
        },
        {
            "createdTime": "2024-03-19T11:15:02.000+08:00",
            "createdBy": "system",
            "updatedTime": "2024-03-19T11:15:02.000+08:00",
            "updatedBy": "system",
            "delFlag": 0,
            "id": 6,
            "sceneName": "翻译专家",
            "sceneDetail": "专业的多国语言翻译",
            "sceneContent": "你现在是一名翻译专家，我给你原文，然后根据我需要的语言进行翻译"
        }
    ],
    "code": 200,
    "msg": "查询成功"
}
    */
  });
}

// 对话列表  
const getAiAssistantChatContent = () => {
  request({
    method: "post",
    url: `/biz/aiAssistantChatContent/list`,
    data: {
      chatId: chatId.value
    },
  }).then((res: any) => {
    if (res.rows) {
      chatList.value = res.rows
      console.log('chatList 对话列表', chatList.value)

      /*  
      {
    "total": 4,
    "rows": [
        {
            "createdTime": "2024-04-12T16:28:03.000+08:00",
            "createdBy": "358",
            "updatedTime": "2024-04-12T16:28:03.000+08:00",
            "updatedBy": "358",
            "delFlag": 0,
            "id": 1144,
            "chatId": 427,
            "role": "user",
            "num": 0,
            "content": "你现在是一名中新闻记者，我给你内容，你要根据内容总结成一份新闻简报"
        },
        {
            "createdTime": "2024-04-12T16:28:07.000+08:00",
            "createdBy": "358",
            "updatedTime": "2024-04-12T16:28:07.000+08:00",
            "updatedBy": "358",
            "delFlag": 0,
            "id": 1145,
            "chatId": 427,
            "role": "assistant",
            "num": 1,
            "content": "当然，我会尽力根据您提供的内容来撰写一份新闻简报。请您提供相关的信息和细节，以便我能准确地完成这项工作。"
        },
        {
            "createdTime": "2024-04-12T16:29:06.000+08:00",
            "createdBy": "358",
            "updatedTime": "2024-04-12T16:29:06.000+08:00",
            "updatedBy": "358",
            "delFlag": 0,
            "id": 1146,
            "chatId": 427,
            "role": "user",
            "num": 2,
            "content": "人工智能\n"
        },
        {
            "createdTime": "2024-04-12T16:29:30.000+08:00",
            "createdBy": "358",
            "updatedTime": "2024-04-12T16:29:30.000+08:00",
            "updatedBy": "358",
            "delFlag": 0,
            "id": 1147,
            "chatId": 427,
            "role": "user",
            "num": 3,
            "content": "1"
        }
    ],
    "code": 200,
    "msg": "查询成功"
}
      */
    }
  });
}

</script>
<style lang="scss" scoped>
.aiContent {
  width: 100%;
  height: 100%;
  border: inherit;

  .Aileft {
    background-color: #F8FAFF;
    height: 770px;
    position: relative;
    padding: 26px;

    .addNewChat {
      display: flex;
      justify-content: space-around;
      align-items: center;
    }

    .leftList {
      height: 680px;
      overflow-y: auto;

      /* 当内容超出高度时显示垂直滚动条 */
      .leftTitle {
        font-size: 14px;
        color: #8F8F90;
        margin: 20px 0px 0px 0px;
      }

      .leftListChat {
        .leftListChatItem {
          background-color: #fff;
          margin-top: 20px;
          display: flex;
          justify-content: space-between;
          align-items: center;
          padding: 0px 10px 0px 10px;

          .iconList {
            display: flex;

            img {
              margin-left: 10px;
            }
          }
        }

        .leftListChatItem:hover {
          background-color: #eef1fc;
        }
      }
    }

    .noHistory {
      position: absolute;
      top: 45%;
      left: 50%;
      transform: translate(-50%, -50%);
      display: flex;
      flex-direction: column;
      align-items: center;

      span {
        color: #7F7F7F;
        font-size: 14px;
      }
    }
  }

  .AiRight {
    background-color: #fff;
    height: 770px;
    padding: 16px 26px 26px 26px;

    .chatContent {
      height: 620px;
      overflow-y: scroll;
      margin-bottom: 10px;

      .aiAssistant {
        background-color: #f7f9ff;
        padding: 20px 20px 20px 20px;
        border-radius: 6px;

        .aiNav {
          display: flex;
          justify-content: space-between;
          font-size: 15px;

          .aiTitle {}

          .aiChange {
            display: flex;
            align-items: center;
            cursor: pointer;
            color: #0a48ef;
          }
        }

        .aiCardContent {
          display: flex;
          flex-wrap: wrap;
          justify-content: space-between;

          /* 在主轴方向上均匀分配间隔，使得每行可以容纳3个元素 */
          .aiCard {
            background-color: #fff;
            width: 246px;
            height: 70px;
            display: flex;
            align-items: center;
            margin-top: 20px;
            border-radius: 4px;
            padding: 12px 12px 12px 12px;
            cursor: pointer;

            .aiCardLeftBg {
              position: relative;
              width: 32px;
              height: 32px;
              background-color: #E8F2FF;
              border-radius: 4px;

              .aiCardLeft {
                position: absolute;
                left: 26%;
                top: 20%;
                width: 36px;
              }

            }

            .aiCardRight {
              margin-left: 12px;

              .aiCardRightTitle {
                font-size: 15px;
                font-weight: bold;
              }

              .aiCardRightPrompt {
                font-size: 12px;
                color: #8b8b8b;
              }
            }
          }
        }

      }

      .chatDialog {

        .chatUser {
          display: flex;

          .chatUserLeft {
            width: 38px;
            height: 38px;
          }

          .chatUserRight {
            width: 100%;
            background-color: #F2F2F2;
            border-radius: 6px;
            margin-left: 16px;
            font-size: 14px;
          }
        }

        .chatAssistant {
          display: flex;
          margin-top: -3px;

          .assistantLeft {
            width: 38px;
            height: 38px;
          }

          .assistantRight {
            width: 100%;
            background-color: #F8FAFF;
            // background-color: red;
            position: relative;
            border-radius: 6px;
            margin-left: 16px;
            font-size: 14px;

            .assLoading {
              display: flex;
              align-items: center;
              padding-right: 16px;

              img {
                width: 30px;
                height: 30px;
              }

              span {}
            }

            .assistantEdit {
              margin-top: 14px;
              margin-left: -4px;
              display: flex;
              align-items: center;
              width: 100px;

              img {
                width: 22px;
                height: 22px;
                cursor: pointer;
              }

              .imgLeft {
                margin-left: 11px;
              }
            }

            .assistantStop {
              display: flex;
              height: 28px;
              width: 102px;
              align-items: center;
              position: absolute;
              bottom: -33px;
              left: 0px;
              border-radius: 4px;
              padding: 4px 12px 4px 12px;
              border: 1px solid #D9D9D9;
              cursor: pointer;


              .stopIcon {
                width: 12px;
                height: 12px;
              }

              .stopText {
                margin-left: 8px;
                color: #555555;
              }
            }
          }
        }
      }
    }

    .chatFooter {
      .chatInput {
        height: 80px;
        border: 1px solid #0a48ef;
        border-radius: 6px;
        display: flex;
        justify-content: space-between;

        .chatTextarea {
          align-self: flex-start;
        }

        .sendBtn {
          align-self: flex-end;
          margin-bottom: 6px;
        }
      }

      .chatTip {
        color: #7F7F7F;
        text-align: center;
        margin-top: 8px;
        margin-bottom: 8px;
      }
    }
  }
}






.aiSpanNoBlue {
  font-family: Source Han Sans CN;
  font-size: 14px;
  line-height: normal;
  font-weight: normal;
  letter-spacing: 0em;
  letter-spacing: 0em;
  color: #0a48ef;
  width: 100%;
  height: 36px;
  border-radius: 6px;
  opacity: 1;
  box-sizing: border-box;
  border: 1px solid #0a48ef;
  display: flex;
  align-items: center;
  justify-content: center;
  cursor: pointer;
}

.aiSpanNoBlueS {
  font-family: Source Han Sans CN;
  font-size: 14px;
  line-height: normal;
  font-weight: normal;
  letter-spacing: 0em;
  letter-spacing: 0em;
  width: 100%;
  height: 45px;
  border-radius: 6px;
  opacity: 1;
  box-sizing: border-box;
  cursor: pointer;
}


// 选中
.aiSpanActive {
  color: #0a48ef;
  border: 1px solid #0a48ef;
}

.aiSpanActiveBG {
  background-color: #0a48ef;
  color: #fff;
  border: 1px solid #0a48ef;
}

.leftIcon {
  width: 20px;
  height: 20px;
}

.leftList::-webkit-scrollbar {
  width: 4px;
  /* 调整滚动条的宽度 */
}

.leftList::-webkit-scrollbar-thumb {
  background-color: #d4d4d4;
  /* 设置滚动条滑块的颜色 */
}


.sendBtn {
  font-family: Source Han Sans CN;
  font-size: 14px;
  font-weight: 500;
  line-height: 36px;
  text-align: center;
  letter-spacing: 0em;
  color: #fff;
  width: 94px;
  height: 36px;
  border-radius: 6px;
  opacity: 1;
  box-sizing: border-box;
  margin-right: 12px;
  background-color: #0a48ef;
  cursor: pointer;
}

.marginBottom {
  margin-bottom: 27px;
}

.paddingEx {
  padding: 14px;
}

.ellipsis-title {
  white-space: nowrap;
  /* 禁止换行 */
  overflow: hidden;
  /* 隐藏超出容器的内容 */
  // text-overflow: ellipsis;
  /* 当内容溢出时显示省略号 */
  width: 100%;
  /* 或者设置一个固定的宽度，确保内容达到溢出条件 */
}

.sensitive {
  height: 80px;
  display: flex;
  justify-content: center;
  /* 水平居中 */
  align-items: flex-end;
  /* 垂直居底 */
  padding-bottom: 10px;
  color: #3952F1;
  font-size: 18px;
  font-weight: 600;
  cursor: pointer;
}

.qrcodeBoxAI {
  padding: 0;
  width: 420px;
  height: 406px;
  background: #ffffff;
  box-sizing: border-box;
  border: 1px solid #cecfd3;
  backdrop-filter: blur(5.44px);
  text-align: center;
  box-shadow: 0px 2px 8px 0px rgba(0, 0, 0, 0.1);
}

.footerBtn {
  position: absolute;
  bottom: 40px;
  left: 0px;
  right: 40px;

  section {
    display: flex;
    align-items: center;
    justify-content: center;
    cursor: pointer;
  }

  .section1 {
    font-family: Source Han Sans CN;
    font-size: 14px;
    font-weight: 500;
    line-height: normal;
    text-align: center;
    letter-spacing: 0em;
    color: #0a48ef;
    width: 94px;
    height: 36px;
    border-radius: 6px;
    opacity: 1;
    box-sizing: border-box;
    margin-right: 12px;
    border: 1px solid #0a48ef;
  }

  .section2 {
    font-family: Source Han Sans CN;
    font-size: 14px;
    font-weight: 500;
    line-height: normal;
    text-align: center;
    width: 94px;
    height: 36px;
    border-radius: 6px;
    margin-left: 12px;
    margin-right: -11px;
    opacity: 1;
    box-sizing: border-box;
    background: #0a48ef;
    letter-spacing: 0em;
    color: #ffffff;
  }
}

.qrcodeBoxVioce {
  width: 580px;
  height: 484px;
  background: #ffffff;
  box-sizing: border-box;
  padding: 40px;
  border-radius: 20px;
  border: 1px solid #cecfd3;
  backdrop-filter: blur(5.44px);
  box-shadow: 0px 2px 8px 0px rgba(0, 0, 0, 0.1);
  position: relative;

  .vioceBtn {
    width: 180px;
    height: 180px;
    opacity: 1;
    border-radius: 50%;
    margin: auto;
    margin-top: 40px;
    /* 固定色10 */
    background: rgba(57, 82, 241, 0.1);
    position: relative;

    img {
      width: 46px;
      height: 46px;
      position: absolute;
      left: 50%;
      top: 44%;
      transform: translate(-50%, -50%);
    }

    p {
      font-family: 思源黑体;
      font-size: 15px;
      font-weight: normal;
      line-height: 18px;
      text-align: center;
      letter-spacing: 0px;
      position: absolute;
      top: 110px;
      transform: translateX(-50%);
      left: 50%;
      cursor: pointer;
      /* 固定色 */
      color: #0a48ef;
    }
  }

  .actionVioce {
    font-family: Source Han Sans CN;
    font-size: 16px;
    font-weight: 500;
    line-height: 18px;
    text-align: center;
    letter-spacing: 0px;
    /* 字40 */
    color: rgba(0, 0, 0, 0.4);
    margin-top: 16px;
  }

  .vioceTime {
    width: 298px;
    height: 36px;
    border-radius: 30px;
    opacity: 1;
    text-align: center;
    margin: auto;
    display: flex;
    align-items: center;
    justify-content: center;
    margin-top: 20px;
    /* 固定色10 */
    background: rgba(57, 82, 241, 0.1);

    img {
      width: 20px;
      cursor: pointer;
      height: 20px;
    }

    span {
      font-family: Source Han Sans CN;
      font-size: 16px;
      font-weight: normal;
      line-height: 36px;
      text-align: center;
      letter-spacing: 0em;
      font-feature-settings: "kern" on;
      color: #090909;
      margin-left: 10px;
      margin-right: 10px;
    }
  }

  .footerBtn {
    position: absolute;
    bottom: 40px;
    left: 50%;
    transform: translateX(-50%);

    section {
      display: flex;
      align-items: center;
      justify-content: center;
      cursor: pointer;
    }

    .section1 {
      font-family: Source Han Sans CN;
      font-size: 14px;
      font-weight: 500;
      line-height: normal;
      text-align: center;
      letter-spacing: 0em;
      color: #0a48ef;
      width: 94px;
      height: 36px;
      border-radius: 6px;
      opacity: 1;
      box-sizing: border-box;
      margin-right: 12px;
      border: 1px solid #0a48ef;
    }

    .section2 {
      font-family: Source Han Sans CN;
      font-size: 14px;
      font-weight: 500;
      line-height: normal;
      text-align: center;
      width: 94px;
      height: 36px;
      border-radius: 6px;
      margin-left: 12px;
      opacity: 1;
      box-sizing: border-box;
      background: #0a48ef;
      letter-spacing: 0em;
      color: #ffffff;
    }
  }
}

.del-footer {
  position: absolute;
  bottom: 36px;
  left: 55%;
  display: flex;
  align-items: center;
  justify-content: center;
  cursor: pointer;
}

.cancal {
  font-family: Source Han Sans CN;
  font-size: 14px;
  font-weight: 500;
  line-height: 36px;
  text-align: center;
  letter-spacing: 0em;
  color: #0a48ef;
  width: 94px;
  height: 36px;
  border-radius: 6px;
  opacity: 1;
  box-sizing: border-box;
  margin-right: 12px;
  border: 1px solid #0a48ef;
}

.confirm {
  font-family: Source Han Sans CN;
  font-size: 14px;
  font-weight: 500;
  line-height: 36px;
  text-align: center;
  width: 94px;
  height: 36px;
  border-radius: 6px;
  margin-left: 12px;
  opacity: 1;
  box-sizing: border-box;
  background: #0a48ef;
  letter-spacing: 0em;
  color: #ffffff;
}

.titleClass {
  margin-left: 20px;
  font-size: 16px;
}


.chatContent::-webkit-scrollbar {
  width: 5px;
  display: none;
}

.chatContent::-webkit-scrollbar-thumb {
  background-color: #d4d4d4;
}
</style>
