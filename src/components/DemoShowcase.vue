<template>
  <section class="glass-card showcase-container">
    <div class="showcase-header">
      <div class="showcase-title-group">
        <h2 class="showcase-title">辩论对抗演示案例库 (30 精选样例)</h2>
        <p class="showcase-subtitle">体验不同立场与五大流派的思维交锋，点击“一键同款开辩”即可即刻对决</p>
      </div>
      <div class="showcase-badge">思想交锋 · 免费体验</div>
    </div>

    <!-- 搜索与分类筛选 -->
    <div class="showcase-filter-bar">
      <div class="category-tabs">
        <button 
          v-for="cat in categories" 
          :key="cat"
          class="category-tab"
          :class="{ active: currentCategory === cat }"
          @click="currentCategory = cat"
        >
          {{ cat }}
        </button>
      </div>
      <div class="search-input-wrapper">
        <input 
          v-model="searchQuery"
          type="text"
          placeholder="搜索辩题、立场或对手流派..."
          class="search-input"
        />
      </div>
    </div>

    <!-- 样例列表格 Grid -->
    <div class="sample-grid">
      <div 
        v-for="sample in paginatedSamples" 
        :key="sample.id" 
        class="sample-card"
      >
        <div class="sample-card-header">
          <span class="topic-category-tag">{{ sample.category }}</span>
          <span class="position-tag" :class="sample.positionType">{{ sample.position }}</span>
        </div>
        <div class="sample-original">
          <span class="sample-label">辩题：</span>{{ sample.topic }}
        </div>
        <div class="sample-style-tag-line">
          <span class="sample-label">对手流派：</span><span class="style-name">{{ sample.style }}</span>
        </div>
        <div class="sample-rewritten">
          <span class="sample-label">交锋纪要：</span>{{ sample.summary }}
        </div>
        <div class="sample-card-footer">
          <button class="use-sample-btn" @click="$emit('use-sample', sample.topic)">
            一键同款开辩
          </button>
        </div>
      </div>
    </div>

    <!-- 空状态提示 -->
    <div v-if="filteredSamples.length === 0" class="empty-showcase">
      未找到匹配的辩论样例，请尝试切换分类或重置搜索关键词。
    </div>

    <!-- 分页组件 -->
    <div v-if="filteredSamples.length > pageSize" class="pagination-bar">
      <button 
        class="page-btn" 
        :disabled="currentPage === 1"
        @click="currentPage--"
      >
        上一页
      </button>
      <span class="page-info">第 {{ currentPage }} / {{ totalPages }} 页 (共 {{ filteredSamples.length }} 条)</span>
      <button 
        class="page-btn" 
        :disabled="currentPage === totalPages"
        @click="currentPage++"
      >
        下一页
      </button>
    </div>
  </section>
</template>

<script setup lang="ts">
import { ref, computed, watch } from 'vue';

defineEmits<{
  (e: 'use-sample', text: string): void;
}>();

const categories = ['全部', '职场生存', '科技伦理', '社会热点', '生活哲学', '赛博朋克'];
const currentCategory = ref('全部');
const searchQuery = ref('');
const currentPage = ref(1);
const pageSize = 6;

interface DebateSample {
  id: number;
  topic: string;
  category: string;
  position: string;
  positionType: 'pro' | 'con' | 'neutral';
  style: string;
  summary: string;
}

// 精选 30 条辩论对抗案例
const raw30Samples: DebateSample[] = [
  {
    id: 1,
    topic: '人工智能创作是否具备人类艺术的真正灵魂？',
    category: '科技伦理',
    position: '正方：具备灵魂',
    positionType: 'pro',
    style: '九巨擘圆桌辩论',
    summary: '马斯克与乔布斯激烈论战：马斯克认为艺术本质是信息熵降解与神经网络冲动，乔布斯反驳极致的体验来自人类不完美的灵魂与情感羁绊。'
  },
  {
    id: 2,
    topic: '上班摸鱼被领导抓到，该不该当场承认错误？',
    category: '职场生存',
    position: '反方：绝不承认',
    positionType: 'con',
    style: '奇葩杠精',
    summary: '杠精驳斥：我不是在摸鱼，我是在进行脑神经系统发散思维训练！如果我一直盯着屏幕，怎么为公司进行跨界创新？'
  },
  {
    id: 3,
    topic: '996 工作制究竟是奋斗者的福报还是资本剥削？',
    category: '职场生存',
    position: '裁判：客观中立',
    positionType: 'neutral',
    style: '儒雅学者',
    summary: '学者论证：论语云‘欲速则不达’。短期的加班虽能积累资本，但若损耗人力资源的长期再生能力，无异于竭泽而渔。'
  },
  {
    id: 4,
    topic: '买房还是租房更符合当代大都市青年发展？',
    category: '生活哲学',
    position: '正方：支持买房',
    positionType: 'pro',
    style: '暴躁老哥',
    summary: '暴躁老哥反问：租房十年你给房东还房贷，房子还是别人的！房产不仅是资产锚点，更是你在这个城市的归属感防线！'
  },
  {
    id: 5,
    topic: '大模型全面应用后，程序员这个职业是否会消亡？',
    category: '赛博朋克',
    position: '反方：不会消亡',
    positionType: 'con',
    style: '赛博朋克',
    summary: '系统拆解：代码编写占软件工程权重仅为 30%，需求契约提炼、架构边界治理与容错演进仍需人类碳基大脑进行最后校验。'
  },
  {
    id: 6,
    topic: '当今社会，选择高薪不喜欢的工作还是低薪喜欢的工作？',
    category: '职场生存',
    position: '正方：高薪不喜欢',
    positionType: 'pro',
    style: '九巨擘圆桌辩论',
    summary: '贝索斯与比尔盖茨对话：贝索斯强调 Day 1 现金流是探索兴趣的前提；盖茨指出用高薪积累杠杆，未来才能自由资助真爱项目。'
  },
  {
    id: 7,
    topic: '年轻人应该存钱理财还是趁年轻多消费体验人生？',
    category: '生活哲学',
    position: '反方：支持消费',
    positionType: 'con',
    style: '奇葩杠精',
    summary: '杠精偷换概念：钱存银行里只是个数字，通货膨胀天天吃掉你的购买力，现在不花掉，难道留给三千年后的考古学家当纪念品？'
  },
  {
    id: 8,
    topic: '婚前是否应该强制进行个人财产明晰与协议签署？',
    category: '社会热点',
    position: '正方：支持签署',
    positionType: 'pro',
    style: '儒雅学者',
    summary: '学者引经据典：君子丑话说到前头，亲丑不亲财。理性的契约恰恰是对感性婚姻最深层的敬畏与保障。'
  },
  {
    id: 9,
    topic: '遇到不公平的职场潜规则，应该勇于揭发还是顺应自保？',
    category: '职场生存',
    position: '反方：顺应自保',
    positionType: 'con',
    style: '暴躁老哥',
    summary: '老哥直言：留得青山在不怕没柴烧！硬刚前先看清自己手里的筹码，没有实力就冲上去那是送人头！'
  },
  {
    id: 10,
    topic: '自动驾驶汽车在不可避免撞击时，应优先保护车内乘客还是行人？',
    category: '科技伦理',
    position: '裁判：客观中立',
    positionType: 'neutral',
    style: '赛博朋克',
    summary: '算法博弈：电车难题在代码层的映射。从博弈论与系统效用最大化推导，无论偏向哪一方都会导致买家或公众的博弈崩溃。'
  },
  {
    id: 11,
    topic: '内卷严重的时代，‘躺平’与‘平替’是否是一种合理的自我救赎？',
    category: '生活哲学',
    position: '正方：合理救赎',
    positionType: 'pro',
    style: '九巨擘圆桌辩论',
    summary: '柏拉图与秦始皇辩论：柏拉图赞许退回洞穴寻找内心安宁；秦始皇怒斥躺平乃无志之士，大一统时代唯有争先方能图强。'
  },
  {
    id: 12,
    topic: '生成式 AI 编写的代码直接上线生产环境，是否不可接受？',
    category: '赛博朋克',
    position: '反方：完全接受',
    positionType: 'con',
    style: '赛博朋克',
    summary: '架构分析：通过自动化单元测试、静态类型校验与 CI/CD 契约防线，AI 代码的缺陷率远低于疲劳的碳基程序员。'
  },
  {
    id: 13,
    topic: '朋友圈等社交媒体是否加剧了现代人的焦虑与虚荣？',
    category: '社会热点',
    position: '正方：加剧焦虑',
    positionType: 'pro',
    style: '儒雅学者',
    summary: '学者解构：虚荣乃镜像心理学之产物。人们展出的皆是剪辑后的光鲜瞬间，盲目攀比无异于自寻烦恼。'
  },
  {
    id: 14,
    topic: '为了绩效指标，在汇报 PPT 中包装数据是否属于合规技巧？',
    category: '职场生存',
    position: '反方：不合规',
    positionType: 'con',
    style: '暴躁老哥',
    summary: '老哥批驳：包装数据就是造假！PPT 做得再漂亮，实际业务没有产出，迟早会被大老板一枪崩掉！'
  },
  {
    id: 15,
    topic: '人生的终极意义是追求快乐，还是追求自我价值实现？',
    category: '生活哲学',
    position: '裁判：客观中立',
    positionType: 'neutral',
    style: '九巨擘圆桌辩论',
    summary: '爱因斯坦与特斯拉论道：爱因斯坦认为探索宇宙规律带来的快乐即是价值；特斯拉则强调将能量转化为人类文明福祉。'
  },
  {
    id: 16,
    topic: '面对不合理的客户需求，服务方是否应该无条件迎合？',
    category: '职场生存',
    position: '反方：绝不迎合',
    positionType: 'con',
    style: '九巨擘圆桌辩论',
    summary: '乔布斯观点：客户根本不知道自己要什么，直到你把它摆在他们面前！无脑迎合只会产出平庸的垃圾。'
  },
  {
    id: 17,
    topic: '亲密关系中，应不应该向另一半完全坦白所有私人历史？',
    category: '生活哲学',
    position: '反方：保留隐私',
    positionType: 'con',
    style: '奇葩杠精',
    summary: '杠精反问：连国家都有最高机密防线，你难道要把三岁尿裤子的黑历史也写进结婚誓言里吗？适当保留才是美感！'
  },
  {
    id: 18,
    topic: '如果脑机接口能够让人类永生在虚拟世界，你是否愿意接入？',
    category: '赛博朋克',
    position: '正方：愿意接入',
    positionType: 'pro',
    style: '赛博朋克',
    summary: '系统论证：肉体碳基衰变为必然熵增，将意识上传至数字永生网络，可实现知识与思维维度的永久延续。'
  },
  {
    id: 19,
    topic: '传统行业数字化转型，淘汰跟不上技术的老员工是否冷酷？',
    category: '社会热点',
    position: '裁判：客观中立',
    positionType: 'neutral',
    style: '儒雅学者',
    summary: '学者中庸：天地不仁，以万物为刍狗。技术变革浪潮滚滚，企业既需追求效率，亦当尽到社会再培训之责任。'
  },
  {
    id: 20,
    topic: '团队项目中，过程努力但结果很差，是否应该给予奖励？',
    category: '职场生存',
    position: '正方：给予奖励',
    positionType: 'pro',
    style: '暴躁老哥',
    summary: '老哥亮明立场：只要大家方向没走偏，战术拼尽全力，输了也得给兄弟们发慰问金！不然以后谁还敢为你打硬仗？'
  },
  {
    id: 21,
    topic: '自律真的能带来自由，还是自我施加的新枷锁？',
    category: '生活哲学',
    position: '反方：是新枷锁',
    positionType: 'con',
    style: '奇葩杠精',
    summary: '杠精诡辩：天天定闹钟打卡做计划，这叫哪门子自由？真正的自由是想睡到几点睡到几点，想吃垃圾食品就吃垃圾食品！'
  },
  {
    id: 22,
    topic: '开源软件项目是否应该对商业巨头免费无限制使用？',
    category: '科技伦理',
    position: '正方：完全免费',
    positionType: 'pro',
    style: '九巨擘圆桌辩论',
    summary: '比尔盖茨与扎克伯格交锋：盖茨强调生态建设与规模效应；扎克伯格认为开放源代码能加速全人类技术迭代。'
  },
  {
    id: 23,
    topic: '在大城市苦苦挣扎，还是回老家小县城安稳过一生？',
    category: '生活哲学',
    position: '反方：回老家',
    positionType: 'con',
    style: '白话易懂',
    summary: '白话分析：大城市的机会虽多，但高昂的生活成本吞噬了幸福感。老家生活节奏适中，人情味浓，生活质量反倒更高。'
  },
  {
    id: 24,
    topic: '如果科技发展能克隆逝去的亲人，这种技术是否应当合法化？',
    category: '科技伦理',
    position: '反方：严禁合法',
    positionType: 'con',
    style: '儒雅学者',
    summary: '学者痛陈：生老病死乃天道轮回。克隆出来的仅是肉体皮囊，亲人的灵魂与不可复制的记忆早已消逝。'
  },
  {
    id: 25,
    topic: '职场中‘会干的不如会吹的’，这种现象该不该被摒弃？',
    category: '职场生存',
    position: '正方：应当摒弃',
    positionType: 'pro',
    style: '暴躁老哥',
    summary: '老哥痛骂：吹牛逼吹得天花乱坠，最后落地烂摊子还不是老实人来收拾！少搞形式主义，拿结果说话！'
  },
  {
    id: 26,
    topic: '在宇宙尺度下，人类文明的一切努力是否都是毫无意义的？',
    category: '赛博朋克',
    position: '裁判：客观中立',
    positionType: 'neutral',
    style: '赛博朋克',
    summary: '热力学解读：宇宙终归热寂。但文明在局域降低熵值、创造秩序的过程本身，就是微观粒子最壮丽的涌现。'
  },
  {
    id: 27,
    topic: '相亲结婚是否能带来比自由恋爱更稳定的婚姻？',
    category: '社会热点',
    position: '正方：相亲更稳定',
    positionType: 'pro',
    style: '白话易懂',
    summary: '实用分析：相亲经过了门当户对与家庭背景的前置筛选，经济底座与生活习惯匹配度更高，婚姻抗风险能力更强。'
  },
  {
    id: 28,
    topic: '为了追求艺术或者科学的终极突破，是否可以打破常规伦理道德？',
    category: '科技伦理',
    position: '反方：绝不可以',
    positionType: 'con',
    style: '九巨擘圆桌辩论',
    summary: '秦始皇与柏拉图碰撞：秦始皇主张为了大业可不择手段；柏拉图严正宣告失去道德底线的追求终将毁灭文明自身。'
  },
  {
    id: 29,
    topic: '电子竞技是否属于正规体育运动项目？',
    category: '社会热点',
    position: '正方：属于体育',
    positionType: 'pro',
    style: '奇葩杠精',
    summary: '杠精抢答：下围棋国际象棋坐在那里不动也是体育，凭什么我手速每分钟四百下按键盘就不能算体育？'
  },
  {
    id: 30,
    topic: '全自动化 AI 时代，人类最不可被替代的核心能力是什么？',
    category: '赛博朋克',
    position: '裁判：客观中立',
    positionType: 'neutral',
    style: '九巨擘圆桌辩论',
    summary: '九大佬共识：爱、共情力、对未知的审美好奇心、以及在不确定性面前勇于承担后果的道德责任感。'
  }
];

const samples = ref<DebateSample[]>(raw30Samples);

const filteredSamples = computed(() => {
  return samples.value.filter(s => {
    const matchCat = currentCategory.value === '全部' || s.category === currentCategory.value;
    const matchQuery = !searchQuery.value.trim() || 
      s.topic.includes(searchQuery.value) || 
      s.position.includes(searchQuery.value) || 
      s.style.includes(searchQuery.value) ||
      s.summary.includes(searchQuery.value);
    return matchCat && matchQuery;
  });
});

const totalPages = computed(() => Math.ceil(filteredSamples.value.length / pageSize) || 1);

const paginatedSamples = computed(() => {
  const start = (currentPage.value - 1) * pageSize;
  return filteredSamples.value.slice(start, start + pageSize);
});

watch([currentCategory, searchQuery], () => {
  currentPage.value = 1;
});
</script>
