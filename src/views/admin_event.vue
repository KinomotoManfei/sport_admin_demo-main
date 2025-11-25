<template>
  <div class="admin-container">
    <!-- 顶部标题标题栏 -->
    <div class="header">
      <h1 class="header-title">体育赛事系统管理员界面</h1>
      <div class="user-info">你好, xxx</div>
    </div>

    <!-- 主体布局 -->
    <div class="main-layout">
      <!-- 侧边导航栏 -->
      <div class="sidebar">
        <div
          class="menu-item"
          :class="{ active: activeMenu === 'info' }"
          @click="handleMenuClick('info', '/admin')"
        >
          <span class="menu-icon">&#xe63c;</span>
          <span class="menu-text">赛事基本信息发布</span>
        </div>

        <!-- 赛事编排管理 → /arrange -->
        <div
          class="menu-item"
          :class="{ active: activeMenu === 'arrange' }"
          @click="handleMenuClick('arrange', '/arrange')"
        >
          <span class="menu-icon">&#xe62a;</span>
          <span class="menu-text">赛事编排管理</span>
        </div>

        <!-- 参赛队伍管理 → /team -->
        <div
          class="menu-item"
          :class="{ active: activeMenu === 'team' }"
          @click="handleMenuClick('team', '/team')"
        >
          <span class="menu-icon">&#xe61e;</span>
          <span class="menu-text">参赛队伍管理</span>
        </div>

        <!-- 比赛规则配置 → /rule -->
        <div
          class="menu-item"
          :class="{ active: activeMenu === 'rule' }"
          @click="handleMenuClick('rule', '/rule')"
        >
          <span class="menu-icon">&#xe60e;</span>
          <span class="menu-text">比赛规则配置</span>
        </div>

        <!-- 成绩录入与管理 → /grade -->
        <div
          class="menu-item"
          :class="{ active: activeMenu === 'score' }"
          @click="handleMenuClick('score', '/grade')"
        >
          <span class="menu-icon">&#xe628;</span>
          <span class="menu-text">成绩录入与管理</span>
        </div>
      </div>

      <!-- 内容区域 -->
      <div class="content-area">
        <!-- 比赛类型选择 -->
        <div class="type-section">
          <label class="type-label">比赛类型:</label>
          <div class="type-buttons">
            <button
              v-for="(type, idx) in matchTypes"
              :key="idx"
              class="type-btn"
              :class="{ active: activeMatchType === type }"
              @click="activeMatchType = type"
            >
              {{ type }}
            </button>
          </div>
        </div>

        <!-- 可编辑赛事信息表格 -->
        <table class="event-table">
          <thead>
            <tr>
              <th>赛事名</th>
              <th>参赛队伍1</th>
              <th>参赛队伍2</th>
              <th>时间</th>
              <th>地点</th>
            </tr>
          </thead>
          <tbody>
            <!-- 循环生成可编辑行（7行数据） -->
            <tr v-for="(row, rowIndex) in tableData" :key="rowIndex">
              <td
                v-for="(cell, colIndex) in row"
                :key="colIndex"
                @click="enterEditMode(rowIndex, colIndex)"
                class="editable-cell"
              >
                <!-- 非编辑状态显示文本 -->
                <span v-if="!isEditing(rowIndex, colIndex)">{{ cell.value || '&nbsp;' }}</span>

                <!-- 编辑状态显示输入框 -->
                <input
                  v-else
                  type="text"
                  v-model="editValue"
                  class="edit-input"
                  @blur="saveEdit(rowIndex, colIndex)"
                  @keyup.enter="saveEdit(rowIndex, colIndex)"
                  @keyup.esc="cancelEdit()"
                  ref="editInput"
                >
              </td>
            </tr>
          </tbody>
        </table>

        <!-- 发布信息按钮 -->
        <button class="publish-btn" @click="publishData">发布信息</button>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      // 侧边菜单数据（保持原有结构）
      menuList: [
        { label: '赛事基本信息发布', icon: '&#xe63c;' },
        { label: '赛事编排管理', icon: '&#xe62a;' },
        { label: '参赛队伍管理', icon: '&#xe61e;' },
        { label: '比赛规则配置', icon: '&#xe60e;' },
        { label: '成绩录入与管理', icon: '&#xe628;' }
      ],
      // 默认选中“赛事编排管理”
      activeMenu: 'arrange',
      // 比赛类型选项
      matchTypes: ['足球', '篮球', '羽毛球', '排球', '水上运动'],
      activeMatchType: '足球',

      // 表格数据（7行5列的可编辑数据结构）
      tableData: Array(7).fill().map(() =>
        Array(5).fill().map(() => ({ value: '' }))
      ),

      // 编辑状态管理
      editing: {
        row: -1,  // 当前编辑的行索引
        col: -1   // 当前编辑的列索引
      },
      editValue: ''  // 编辑框临时值
    }
  },
  methods: {
    // 菜单点击处理
    handleMenuClick(menuKey, path) {
      this.activeMenu = menuKey
      this.$router.push(path)
    },

    // 判断是否处于编辑状态
    isEditing(row, col) {
      return this.editing.row === row && this.editing.col === col
    },

    // 进入编辑模式
    enterEditMode(row, col) {
      // 记录当前单元格的值
      this.editValue = this.tableData[row][col].value
      // 更新编辑状态
      this.editing.row = row
      this.editing.col = col
      // 延迟聚焦输入框（确保DOM已更新）
      this.$nextTick(() => {
        this.$refs.editInput?.focus()
      })
    },

    // 保存编辑内容
    saveEdit(row, col) {
      if (this.isEditing(row, col)) {
        // 更新表格数据
        this.tableData[row][col].value = this.editValue
        // 退出编辑状态
        this.editing.row = -1
        this.editing.col = -1
      }
    },

    // 取消编辑
    cancelEdit() {
      this.editing.row = -1
      this.editing.col = -1
    },

    // 发布信息（处理表格数据）
    publishData() {
      console.log('发布的表格数据:', this.tableData)
      // 实际项目中可对接API：
      // this.$axios.post('/api/publish-event', {
      //   type: this.activeMatchType,
      //   data: this.tableData
      // })
      alert('信息已发布')
    }
  },
  mounted() {
    // 初始化时根据当前路由设置激活菜单
    const currentPath = this.$route.path
    switch (currentPath) {
      case '/admin':
        this.activeMenu = 'info'
        break
      case '/arrange':
        this.activeMenu = 'arrange'
        break
      case '/team':
        this.activeMenu = 'team'
        break
      case '/rule':
        this.activeMenu = 'rule'
        break
      case '/grade':
        this.activeMenu = 'score'
        break
    }
  },
  watch: {
    // 监听路由变化，同步更新激活菜单
    $route(to) {
      switch (to.path) {
        case '/admin':
          this.activeMenu = 'info'
          break
        case '/arrange':
          this.activeMenu = 'arrange'
          break
        case '/team':
          this.activeMenu = 'team'
          break
        case '/rule':
          this.activeMenu = 'rule'
          break
        case '/grade':
          this.activeMenu = 'score'
          break
      }
    }
  }
}
</script>

<style scoped>
/* 全局容器 */
.admin-container {
  width: 100vw;
  height: 100vh;
  border: 0.104vw solid #007bff;
  box-sizing: border-box;
  font-family: "微软雅黑", sans-serif;
}

/* 顶部标题栏 */
.header {
  background-color: #b3e5fc;
  padding: 0.417vw 0.833vw;
  position: relative;
  border-bottom: 0.052vw solid #007bff;
}
.header-title {
  font-size: 0.939vw;
  font-weight: bold;
  text-align: center;
  margin: 0;
}
.user-info {
  position: absolute;
  right: 0.833vw;
  top: 50%;
  transform: translateY(-50%);
  font-size: 0.729vw;
}

/* 主体布局 */
.main-layout {
  display: flex;
  height: calc(100% - 42px);
}

/* 侧边导航栏 */
.sidebar {
  width: 7.813vw;
  background-color: #f0f0f0;
  border-right: 0.052vw solid #007bff;
}
.menu-item {
  padding: 0.521vw 0.625vw;
  font-size: 0.729vw;
  cursor: pointer;
  display: flex;
  align-items: center;
  gap: 0.313vw;
}
.menu-item.active {
  background-color: #e3f2fd;
  color: #2196f3;
  border-left: 0.156vw solid #007bff;
}
.menu-icon {
  font-family: "iconfont";
}

/* 内容区域 */
.content-area {
  flex: 1;
  padding: 1.042vw;
  box-sizing: border-box;
  position: relative;
}

/* 比赛类型区域 */
.type-section {
  display: flex;
  align-items: center;
  margin-bottom: 0.781vw;
}
.type-label {
  font-size: 0.729vw;
  margin-right: 0.521vw;
}
.type-buttons {
  display: flex;
  gap: 0.26vw;
}
.type-btn {
  padding: 0.208vw 0.625vw;
  border: 0.052vw solid #007bff;
  background-color: #fff;
  cursor: pointer;
  font-size: 0.729vw;
}
.type-btn.active {
  background-color: #007bff;
  color: #fff;
}

/* 赛事表格 */
.event-table {
  width: 100%;
  border-collapse: collapse;
  border: 0.052vw solid #000;
}
.event-table th,
.event-table td {
  border: 0.052vw solid #000;
  padding: 0; /* 去除内边距避免编辑框错位 */
  text-align: center;
  font-size: 0.729vw;
  height: 1.875vw; /* 固定行高 */
  min-width: 5.208vw;
}
.event-table th {
  font-weight: normal;
  padding: 0.417vw 0.625vw; /* 表头保留内边距 */
}

/* 可编辑单元格样式 */
.editable-cell {
  cursor: text;
  padding: 0 0.208vw;
}
.editable-cell:hover {
  background-color: #f0f8ff;
}

/* 编辑框样式 */
.edit-input {
  width: 100%;
  height: 100%;
  border: none;
  padding: 0 0.208vw;
  margin: 0;
  outline: 0.052vw solid #007bff;
  background-color: #e3f2fd;
  font-size: 0.729vw;
  box-sizing: border-box;
}

/* 发布信息按钮 */
.publish-btn {
  position: absolute;
  bottom: 1.042vw;
  right: 1.042vw;
  padding: 0.313vw 1.042vw;
  background-color: #ccc;
  border: none;
  cursor: pointer;
  font-size: 0.729vw;
}
</style>
