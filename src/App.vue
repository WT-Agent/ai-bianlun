<template>
  <div class="app-container">
    <!-- 顶部生成成功浮动 Toast -->
    <transition name="fade">
      <div v-if="showSuccessToast" class="top-success-toast">
        <svg xmlns="http://www.w3.org/2000/svg" width="16" height="16" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2.5" stroke-linecap="round" stroke-linejoin="round">
          <polyline points="20 6 9 17 4 12"></polyline>
        </svg>
        <span>辩论对抗解析生成成功！</span>
      </div>
    </transition>

    <!-- 右上角常驻分享按钮 -->
    <button class="floating-share-btn" @click="showShareGuide = true">
      <svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round" class="share-icon">
        <circle cx="18" cy="5" r="3"></circle>
        <circle cx="6" cy="12" r="3"></circle>
        <circle cx="18" cy="19" r="3"></circle>
        <line x1="8.59" y1="13.51" x2="15.42" y2="17.49"></line>
        <line x1="15.41" y1="6.51" x2="8.59" y2="10.49"></line>
      </svg>
      <span>分享</span>
    </button>

    <!-- 顶部 App Header (已移除未登录额度提示栏) -->
    <header>
      <h1>{{ appTitle }}</h1>
      <p>智能 AI 体验引擎 · 设定立场与五大对手流派唇枪舌战</p>
    </header>

    <!-- 活跃动态与使用人数轮播 -->
    <UserTicker />

    <!-- 核心输入与生成卡片 (模块一：一键体验) -->
    <main class="glass-card input-group">
      <div class="selector-group">
        <label class="selector-label">输入您要辩论的主题或冲突焦点</label>
        <textarea 
          v-model="userInput" 
          placeholder="比如：人工智能创作是否具备人类艺术的真正灵魂？上班摸鱼被抓到怎么辩解..."
          rows="4"
        ></textarea>
      </div>

      <div class="selector-group">
        <label class="selector-label">选择您的辩论立场</label>
        <div class="position-selector">
          <button 
            v-for="pos in positionOptions" 
            :key="pos.value"
            class="position-option"
            :class="{ active: activePosition === pos.value }"
            @click="activePosition = pos.value"
          >
            {{ pos.label }}
          </button>
        </div>
      </div>

      <div class="selector-group">
        <label class="selector-label">选择辩论对手流派</label>
        <div class="style-selector">
          <button 
            v-for="style in styleOptions" 
            :key="style.value"
            class="style-option"
            :class="{ active: activeStyle === style.value }"
            @click="activeStyle = style.value"
          >
            {{ style.label }}
          </button>
        </div>
      </div>

      <button 
        class="action-btn" 
        :disabled="loading || !userInput.trim()"
        @click="handleGenerate"
      >
        {{ loading ? '正在由 AI 辩论大师激烈交锋中...' : '开始一键辩论对抗' }}
      </button>

      <!-- 异常提示 -->
      <div v-if="errorMsg" class="error-banner">
        {{ errorMsg }}
      </div>
    </main>

    <!-- 生成结果卡片 (模块二：内容产出与分享) -->
    <section v-if="result || loading" class="glass-card result-section">
      <div class="result-header">
        <div class="result-title-group">
          <span class="result-title">辩论对抗交锋结论</span>
          <span v-if="result" class="success-badge">生成成功</span>
        </div>
        <div class="button-actions">
          <button v-if="result && !isImageProject" class="icon-btn" @click="copyText">
            {{ copied ? '已复制' : '复制辩词' }}
          </button>
          <button v-if="result" class="icon-btn highlight" @click="showShareCard = true">
            生成分享卡片
          </button>
          <a v-if="result && isImageProject" :href="result" target="_blank" download class="icon-btn" style="text-decoration: none;">
            查看原图
          </a>
        </div>
      </div>

      <!-- 加载中骨架屏 -->
      <div v-if="loading" class="skeleton">
        <div class="skeleton-line" style="width: 80%"></div>
        <div class="skeleton-line" style="width: 95%"></div>
        <div class="skeleton-line" style="width: 60%"></div>
        <div class="skeleton-line" style="width: 75%"></div>
      </div>

      <!-- 渲染结果 -->
      <div v-else-if="result">
        <img v-if="isImageProject" :src="result" alt="Generated visual" class="image-output" />
        <div v-else class="output-content">{{ result }}</div>
      </div>
    </section>

    <!-- 演示案例区组件 (模块三：30 条辩论精选案例展示) -->
    <DemoShowcase @use-sample="handleUseSample" />

    <!-- 底部隐私与服务条款链接 -->
    <footer class="footer-links">
      <button class="footer-link-btn" @click="showPrivacy = true">Privacy Policy</button>
      <button class="footer-link-btn" @click="showTerms = true">Terms of Service</button>
      <button class="footer-link-btn" @click="showContact = true">Contact Us</button>
    </footer>

    <!-- 隐私政策弹窗 -->
    <div v-if="showPrivacy" class="modal-overlay" @click.self="showPrivacy = false">
      <div class="modal-content">
        <h3>Privacy Policy</h3>
        <div class="modal-text-content modal-scroll-area">
          <p>我们非常重视您的隐私。您在本应用中输入的所有辩题及立场观点仅用于实时大模型辩论推理，我们不会在服务器端进行永久存储或记录。</p>
          <p>为了记录您的免费额度，本应用会在您的浏览器本地（localStorage）记录试用次数与解锁状态。</p>
        </div>
        <button class="modal-btn" @click="showPrivacy = false">关闭</button>
      </div>
    </div>

    <!-- 服务条款弹窗 -->
    <div v-if="showTerms" class="modal-overlay" @click.self="showTerms = false">
      <div class="modal-content">
        <h3>Terms of Service</h3>
        <div class="modal-text-content modal-scroll-area">
          <p>欢迎使用我们的 AI 辩论擂台微应用。本应用仅用于逻辑思辨训练、观点对抗模拟与娱乐展示，辩词内容不代表平台的立场或观点。</p>
        </div>
        <button class="modal-btn" @click="showTerms = false">关闭</button>
      </div>
    </div>

    <!-- 联系我们弹窗 (自适应高度 + weixin.png & dingtalk.png 展示) -->
    <div v-if="showContact" class="modal-overlay" @click.self="showContact = false">
      <div class="modal-content contact-modal-content">
        <h3>Contact Us</h3>
        <div class="modal-text-content contact-card-body">
          <p>如果您在使用过程中遇到任何问题，或有合作意向，可以通过以下方式联系我们：</p>
          <div class="contact-qr-container">
            <div class="contact-qr-card">
              <img :src="weixinImg" alt="微信联系" class="contact-qr-img" />
              <span class="contact-qr-label">微信联系</span>
            </div>
            <div class="contact-qr-card">
              <img :src="dingtalkImg" alt="钉钉交流" class="contact-qr-img" />
              <span class="contact-qr-label">钉钉交流</span>
            </div>
          </div>
          <p class="contact-email">反馈邮箱: <span style="color: var(--primary-color);">us@wuxian.xyz</span></p>
        </div>
        <button class="modal-btn" @click="showContact = false">关闭</button>
      </div>
    </div>

    <!-- 裂变拦截弹窗 (模块四：裂变机制) -->
    <FissionModal 
      :visible="showFission" 
      :wechat-id="wechatId"
      @unlocked="handleUnlocked"
    />

    <!-- 分享卡片弹窗 (模块二扩展) -->
    <ShareCardModal
      :visible="showShareCard"
      :content="result"
      :wechat-id="wechatId"
      @close="showShareCard = false"
    />

    <!-- 微信 H5 分享引导浮层 -->
    <div v-if="showShareGuide" class="share-guide-overlay" @click="handleShareClose">
      <div class="share-guide-arrow">↗</div>
      <div class="share-guide-content">
        <p>点击右上角菜单 <strong>•••</strong></p>
        <p>选择 <strong>「分享到朋友圈」</strong></p>
        <p class="share-guide-sub">分享这款高效率的 AI 智能微应用</p>
      </div>
    </div>
  </div>
</template>

<script setup lang="ts">
import { ref, computed } from 'vue';
import UserTicker from './components/UserTicker.vue';
import FissionModal from './components/FissionModal.vue';
import DemoShowcase from './components/DemoShowcase.vue';
import ShareCardModal from './components/ShareCardModal.vue';
import appConfig from './config.json';
import weixinImg from '../asset/weixin.png';
import dingtalkImg from '../asset/dingtalk.png';

const appTitle = ref(appConfig.title || '网腾无限AI 辩论擂台');
const wechatId = ref(appConfig.wechatId || 'ai_wuxian_xyz');
const promptTopic = ref(appConfig.promptTopic || '');

const userInput = ref('');
const loading = ref(false);
const errorMsg = ref('');
const result = ref('');
const copied = ref(false);
const showSuccessToast = ref(false);
const showFission = ref(false);
const showPrivacy = ref(false);
const showTerms = ref(false);
const showContact = ref(false);
const showShareGuide = ref(false);
const showShareCard = ref(false);

const handleShareClose = () => {
  showShareGuide.value = false;
  localStorage.setItem('shared_fission', 'true');
};

const getCookie = (name: string): string | null => {
  const nameEQ = name + "=";
  const ca = document.cookie.split(';');
  for (let i = 0; i < ca.length; i++) {
    let c = ca[i];
    while (c.charAt(0) === ' ') c = c.substring(1, c.length);
    if (c.indexOf(nameEQ) === 0) return c.substring(nameEQ.length, c.length);
  }
  return null;
};

const userToken = ref(getCookie('wuxian_session'));
const isLoggedIn = computed(() => !!userToken.value);
const authUsesCount = ref(parseInt(localStorage.getItem('auth_uses') || '0', 10));

const isImageProject = computed(() => {
  return appConfig.type === 'image';
});

// 辩论立场选项
const positionOptions = [
  { label: '正方 (支持)', value: '正方立场：支持并赞成该观点' },
  { label: '反方 (反对)', value: '反方立场：强烈驳斥并反对该观点' },
  { label: '裁判 (中立)', value: '裁判视角：客观中立，剖析双方逻辑瑕疵' },
];
const activePosition = ref(positionOptions[0].value);

// 辩论对手流派选项
const styleOptions = computed(() => {
  if (isImageProject.value) {
    return [
      { label: '写真照片', value: '<photography>' },
      { label: '卡通动漫', value: '<anime>' },
      { label: '水彩画卷', value: '<watercolor>' },
      { label: '插画艺术', value: '<illustration>' },
    ];
  } else {
    return [
      { label: '九巨擘圆桌辩论', value: '九巨擘圆桌辩论大混战：马斯克、比尔盖茨、扎克伯格、贝索斯、乔布斯、柏拉图、爱因斯坦、特斯拉、秦始皇' },
      { label: '奇葩杠精', value: '奇葩杠精流派，无厘头偷换概念强词夺理' },
      { label: '儒雅学者', value: '儒雅学者流派，引经据典温文尔雅字字诛心' },
      { label: '暴躁老哥', value: '暴躁老哥流派，大白话连珠炮刚猛直击漏洞' },
      { label: '赛博朋克', value: '赛博朋克流派，绝对理性数据与系统优化架构' },
    ];
  }
});

const activeStyle = ref(styleOptions.value[0].value);

const isLimitReached = computed(() => {
  if (isLoggedIn.value) {
    return authUsesCount.value >= 15;
  }
  const uses = parseInt(localStorage.getItem('free_uses') || '0', 10);
  const shared = localStorage.getItem('shared_fission') === 'true';
  return uses >= 3 && !shared;
});

const apiEndpoint = import.meta.env.DEV
  ? '/api/local/generate'
  : (import.meta.env.VITE_API_ENDPOINT || 'https://api.wuxian.xyz/api/v1/generate');

const triggerSuccessToast = () => {
  showSuccessToast.value = true;
  setTimeout(() => {
    showSuccessToast.value = false;
  }, 3000);
};

const handleGenerate = async () => {
  if (isLimitReached.value) {
    showFission.value = true;
    return;
  }

  loading.value = true;
  errorMsg.value = '';
  result.value = '';

  try {
    const response = await fetch(apiEndpoint, {
      method: 'POST',
      headers: {
        'Content-Type': 'application/json',
      },
      credentials: 'include',
      body: JSON.stringify({
        taskType: isImageProject.value ? 'image' : 'text',
        prompt: `辩题：${userInput.value}，用户${activePosition.value}，选择对抗对手流派：${activeStyle.value}`,
        style: activeStyle.value
      })
    });

    const data = await response.json();
    if (data.error) {
      errorMsg.value = data.error;
    } else {
      result.value = data.result;
      triggerSuccessToast();
      
      if (isLoggedIn.value) {
        const nextAuthUses = authUsesCount.value + 1;
        localStorage.setItem('auth_uses', nextAuthUses.toString());
        authUsesCount.value = nextAuthUses;
      } else {
        const currentUses = parseInt(localStorage.getItem('free_uses') || '0', 10);
        localStorage.setItem('free_uses', (currentUses + 1).toString());
      }
    }
  } catch (err: any) {
    errorMsg.value = '请求接口失败，请检查网络或本地代理服务。';
  } finally {
    loading.value = false;
  }
};

const handleUseSample = (sampleTopic: string) => {
  userInput.value = sampleTopic;
  window.scrollTo({ top: 0, behavior: 'smooth' });
};

const handleUnlocked = () => {
  showFission.value = false;
  handleGenerate();
};

const copyText = async () => {
  try {
    await navigator.clipboard.writeText(result.value);
    copied.value = true;
    setTimeout(() => {
      copied.value = false;
    }, 2000);
  } catch (err) {
    errorMsg.value = '复制失败，请手动选择复制。';
  }
};
</script>
