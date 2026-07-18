  <div class="app-container">
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

    <header>
      <h1>{{ appTitle }}</h1>
      <p>智能 AI 体验引擎 · 开启创作灵感</p>
    </header>

    <!-- 活跃动态 -->
    <UserTicker />

    <!-- 核心卡片 -->
    <main class="glass-card input-group">
      <div v-if="!result" class="divination-setup">
        <div class="selector-group">
          <label class="selector-label">选择您的立场</label>
          <div class="stance-selector">
            <button 
              class="stance-option" 
              :class="{ active: inquiryStance === '正方' }" 
              @click="inquiryStance = '正方'"
            >
              正方 (支持)
            </button>
            <button 
              class="stance-option" 
              :class="{ active: inquiryStance === '反方' }" 
              @click="inquiryStance = '反方'"
            >
              反方 (反对)
            </button>
            <button 
              class="stance-option" 
              :class="{ active: inquiryStance === '裁判' }" 
              @click="inquiryStance = '裁判'"
            >
              裁判 (评判)
            </button>
          </div>
        </div>

        <div class="selector-group">
          <label class="selector-label">选择辩论对手 / 流派</label>
          <select v-model="activeStyle" class="style-select">
            <option 
              v-for="style in styleOptions" 
              :key="style.value" 
              :value="style.value"
            >
              {{ style.label }}
            </option>
          </select>
        </div>

        <div class="selector-group">
          <label class="selector-label">输入辩论主题 / 辩题</label>
          <textarea 
            v-model="userInput" 
            placeholder="请输入您的辩题，例如：吃粽子到底该吃甜的还是咸的？或者：上班摸鱼被老板抓到，如何理直气壮地为自己辩护？"
          ></textarea>
        </div>

        <button 
          class="action-btn" 
          :disabled="!userInput.trim() || loading" 
          @click="handleGenerate"
        >
          {{ loading ? '辩驳推演中...' : '开启辩论，针锋相对' }}
        </button>
      </div>

      <!-- 结果卡片展示 -->
      <div v-else class="divination-result">
        <div class="result-header">
          <span class="result-title">辩论纪实</span>
          <div class="button-actions">
            <button class="icon-btn" @click="copyText">
              {{ copied ? '已复制' : '复制辩词' }}
            </button>
            <button class="icon-btn" @click="showShareGuide = true">
              分享朋友圈
            </button>
            <button class="icon-btn" @click="resetDebate">
              重新开始
            </button>
          </div>
        </div>

        <div class="ai-response-wrapper">
          <div class="output-content" style="text-align: left;">{{ result }}</div>
        </div>
      </div>

      <!-- 加载状态 -->
      <div v-if="loading" class="ai-loading">
        <div class="spinner"></div>
        <p>正在为您召集对手展开激辩，构建最强辩护逻辑...</p>
      </div>

      <!-- 异常提示 -->
      <div v-if="errorMsg" style="color: var(--accent-color); font-size: 0.85rem; text-align: center; margin-top: 0.5rem;">
        {{ errorMsg }}
      </div>
    </main>

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
          <p>我们非常重视您的隐私。您在本应用中输入的所有辩题及对话细节均仅用于实时大模型分析与生成，我们不会在服务器端进行任何永久存储。</p>
          <p>为了记录您的免费试用额度，本应用会在您的浏览器本地（localStorage）记录试用次数与朋友圈解锁状态。</p>
        </div>
        <button class="modal-btn" @click="showPrivacy = false">关闭</button>
      </div>
    </div>

    <!-- 服务条款弹窗 -->
    <div v-if="showTerms" class="modal-overlay" @click.self="showTerms = false">
      <div class="modal-content">
        <h3>Terms of Service</h3>
        <div class="modal-text-content modal-scroll-area">
          <p>欢迎使用我们的 AI 辩论擂台服务。使用本应用即代表您同意并承诺遵守当地有关人工智能生成内容（AIGC）的法律法规。</p>
          <p>所有辩论生成内容（尤其是“奇葩杠精”和“暴躁老哥”流派）均由 AI 大模型生成以用于文化讨论、逻辑思辨或娱乐效果，并不代表作者立场，且不构成任何实质性法律或人身决策建议。</p>
        </div>
        <button class="modal-btn" @click="showTerms = false">关闭</button>
      </div>
    </div>

    <!-- 联系我们弹窗 -->
    <div v-if="showContact" class="modal-overlay" @click.self="showContact = false">
      <div class="modal-content" style="max-width: 420px;">
        <h3>Contact Us</h3>
        <div class="modal-text-content">
          <p>如果您在使用过程中遇到任何问题，或有合作意向，可以通过微信或钉钉联系我们：</p>
          <div style="display: flex; gap: 1rem; justify-content: center; margin-top: 0.5rem; margin-bottom: 0.5rem; flex-wrap: wrap;">
            <div style="text-align: center;">
              <img :src="weixinImg" alt="微信二维码" style="width: 130px; height: 130px; border-radius: 8px; border: 1px solid rgba(255,255,255,0.1);" />
              <div style="font-size: 0.75rem; margin-top: 0.25rem; color: var(--text-secondary);">微信</div>
            </div>
            <div style="text-align: center;">
              <img :src="dingtalkImg" alt="钉钉二维码" style="width: 130px; height: 130px; border-radius: 8px; border: 1px solid rgba(255,255,255,0.1);" />
              <div style="font-size: 0.75rem; margin-top: 0.25rem; color: var(--text-secondary);">钉钉</div>
            </div>
          </div>
        </div>
        <button class="modal-btn" @click="showContact = false">关闭</button>
      </div>
    </div>

    <!-- 裂变拦截弹窗 -->
    <FissionModal 
      :visible="showFission" 
      :wechat-id="wechatId"
      @unlocked="handleUnlocked"
    />

    <!-- 分享引导浮层 -->
    <div v-if="showShareGuide" class="share-guide-overlay" @click="handleShareClose">
      <div class="share-guide-arrow">↗</div>
      <div class="share-guide-content">
        <p>点击右上角菜单 <strong>•••</strong></p>
        <p>选择 <strong>「分享到朋友圈」</strong></p>
        <p class="share-guide-sub">让好友一起体验智能激辩的魅力</p>
      </div>
    </div>
  </div>
</template>

<script setup lang="ts">
import { ref, computed } from 'vue';
import UserTicker from './components/UserTicker.vue';
import FissionModal from './components/FissionModal.vue';
import appConfig from './config.json';
import weixinImg from '../asset/weixin.png';
import dingtalkImg from '../asset/dingtalk.png';

// 读取动态配置文件
const appTitle = ref(appConfig.title || '网腾无限AI辩论擂台');
const wechatId = ref(appConfig.wechatId || 'ai_wuxian_xyz');
const promptTopic = ref(appConfig.promptTopic || '');

const userInput = ref('');
const inquiryStance = ref('正方');
const loading = ref(false);
const errorMsg = ref('');
const result = ref('');
const copied = ref(false);
const showFission = ref(false);
const showPrivacy = ref(false);
const showTerms = ref(false);
const showContact = ref(false);
const showShareGuide = ref(false);

const styleOptions = [
  { label: '奇葩杠精', value: '奇葩杠精流派：无厘头抬杠、偷换概念、强词夺理。' },
  { label: '儒雅学者', value: '儒雅学者流派：温文尔雅，引经据典，以理服人。' },
  { label: '暴躁老哥', value: '暴躁老哥流派：大白话、反问句句句扎心，风格刚猛热烈。' },
  { label: '赛博朋克', value: '赛博朋克流派：冰冷数据和逻辑优化的绝对理性，客观利弊解析。' },
  { label: '九巨擘圆桌会', value: '九巨擘圆桌会流派：一次性模拟马斯克、比尔盖茨、扎克伯格、贝索斯、乔布斯、柏拉图、爱因斯坦、特斯拉、秦始皇共9人针对该辩题展开激烈的唇枪舌战大混战。' }
];

const activeStyle = ref(styleOptions[0].value);

// 判断是否达到试用限制
const isLimitReached = computed(() => {
  const uses = parseInt(localStorage.getItem('free_uses') || '0', 10);
  const shared = localStorage.getItem('shared_fission') === 'true';
  return uses >= 3 && !shared;
});

// 获取本地或生产 API 请求端点
const apiEndpoint = import.meta.env.DEV
  ? '/api/local/generate'
  : (import.meta.env.VITE_API_ENDPOINT || 'https://api.wuxian.xyz/api/v1/generate');

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
      body: JSON.stringify({
        taskType: 'text',
        prompt: `辩论背景：${promptTopic.value}。辩论辩题：${userInput.value}。用户立场：${inquiryStance.value}。对手辩论流派与作风：${activeStyle.value}`,
        style: activeStyle.value
      })
    });

    const data = await response.json();
    if (data.error) {
      errorMsg.value = data.error;
    } else {
      result.value = data.result;
      
      // 累加免费次数
      const currentUses = parseInt(localStorage.getItem('free_uses') || '0', 10);
      localStorage.setItem('free_uses', (currentUses + 1).toString());
    }
  } catch (err: any) {
    errorMsg.value = '请求接口失败，请检查网络或本地代理服务。';
  } finally {
    loading.value = false;
  }
};

const handleUnlocked = () => {
  showFission.value = false;
  handleGenerate();
};

const handleShareClose = () => {
  showShareGuide.value = false;
  localStorage.setItem('shared_fission', 'true');
};

const resetDebate = () => {
  result.value = '';
  userInput.value = '';
  errorMsg.value = '';
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

<style scoped>
.divination-setup {
  display: flex;
  flex-direction: column;
  gap: 1.25rem;
}

.stance-selector {
  display: flex;
  gap: 0.5rem;
}

.stance-option {
  flex: 1;
  padding: 0.75rem;
  background: rgba(255, 255, 255, 0.05);
  border: 1px solid rgba(255, 255, 255, 0.1);
  border-radius: 8px;
  color: var(--text-secondary);
  font-size: 0.9rem;
  font-weight: bold;
  cursor: pointer;
  transition: all 0.2s ease;
}

.stance-option:hover {
  background: rgba(255, 255, 255, 0.08);
  color: var(--text-primary);
}

.stance-option.active {
  background: var(--primary-color);
  color: #fff;
  border-color: var(--primary-color);
  box-shadow: 0 0 10px rgba(168, 85, 247, 0.3);
}

.style-select {
  width: 100%;
  padding: 0.75rem 1rem;
  background: rgba(255, 255, 255, 0.05);
  border: 1px solid rgba(255, 255, 255, 0.1);
  border-radius: 8px;
  color: var(--text-primary);
  font-family: inherit;
  font-size: 0.9rem;
  outline: none;
  transition: all 0.3s ease;
  cursor: pointer;
  appearance: none;
  -webkit-appearance: none;
  -moz-appearance: none;
  background-image: url("data:image/svg+xml;utf8,<svg xmlns='http://www.w3.org/2000/svg' width='24' height='24' viewBox='0 0 24 24' fill='none' stroke='rgba(255,255,255,0.5)' stroke-width='2' stroke-linecap='round' stroke-linejoin='round'><polyline points='6 9 12 15 18 9'></polyline></svg>");
  background-repeat: no-repeat;
  background-position: right 12px center;
  background-size: 16px;
  padding-right: 2.5rem;
}

.style-select:focus {
  border-color: var(--primary-color);
  box-shadow: 0 0 10px rgba(168, 85, 247, 0.2);
}

.style-select option {
  background-color: #1a1726;
  color: #fff;
}

.divination-result {
  text-align: center;
}

.ai-loading {
  padding: 2rem 1rem;
  display: flex;
  flex-direction: column;
  align-items: center;
  gap: 1rem;
}

.spinner {
  width: 32px;
  height: 32px;
  border: 3px solid rgba(255, 255, 255, 0.1);
  border-top-color: var(--primary-color);
  border-radius: 50%;
  animation: spin 1s linear infinite;
}

@keyframes spin {
  to { transform: rotate(360deg); }
}

.ai-loading p {
  font-size: 0.85rem;
  color: var(--text-secondary);
}

.ai-response-wrapper {
  margin-top: 1.5rem;
}

/* 分享引导浮层 */
.share-guide-overlay {
  position: fixed;
  top: 0;
  left: 0;
  width: 100vw;
  height: 100vh;
  background: rgba(17, 14, 36, 0.9);
  z-index: 1000;
  display: flex;
  flex-direction: column;
  align-items: flex-end;
  padding: 2rem;
  box-sizing: border-box;
  color: #fff;
  cursor: pointer;
}

.share-guide-arrow {
  font-size: 3rem;
  color: var(--primary-color);
  animation: bounce 1s infinite alternate;
  margin-right: 1.5rem;
}

.share-guide-content {
  text-align: center;
  width: 100%;
  margin-top: 2rem;
  display: flex;
  flex-direction: column;
  gap: 0.5rem;
}

.share-guide-content p {
  font-size: 1.2rem;
  margin: 0;
}

.share-guide-sub {
  font-size: 0.9rem;
  color: var(--text-secondary);
  margin-top: 1rem !important;
}

@keyframes bounce {
  from { transform: translateY(0); }
  to { transform: translateY(-10px); }
}

/* 右上角常驻分享按钮 */
.floating-share-btn {
  position: fixed;
  top: 1rem;
  right: 1rem;
  z-index: 99;
  display: flex;
  align-items: center;
  gap: 0.35rem;
  padding: 0.5rem 0.8rem;
  background: rgba(255, 255, 255, 0.08);
  border: 1px solid rgba(255, 255, 255, 0.12);
  border-radius: 20px;
  color: var(--text-primary);
  font-size: 0.8rem;
  font-weight: 500;
  cursor: pointer;
  backdrop-filter: blur(8px);
  -webkit-backdrop-filter: blur(8px);
  transition: all 0.2s ease;
  box-shadow: 0 4px 12px rgba(0, 0, 0, 0.1);
}

.floating-share-btn:hover {
  background: rgba(255, 255, 255, 0.15);
  border-color: var(--primary-color);
  box-shadow: 0 4px 16px rgba(168, 85, 247, 0.2);
}

.share-icon {
  width: 14px;
  height: 14px;
}
</style>
