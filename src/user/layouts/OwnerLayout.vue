<template>
  <div class="hotel-management flex min-h-screen bg-background">
    <!-- 手機版側邊欄遮罩（當側邊欄打開時顯示） -->
    <div
      v-if="showSidebar"
      class="fixed inset-0 bg-black/50 z-40 lg:hidden"
      @click="showSidebar = false"
    ></div>

    <!-- 修改：側邊欄固定定位，不隨頁面滾動 - 與主內容區顏色一致 -->
    <!-- 在小螢幕上默認隱藏，可以通過 showSidebar 控制顯示 -->
    <aside
      :class="[
        'fixed left-0 top-0 h-screen w-64 border-r border-border bg-background flex flex-col z-50 transition-transform duration-300',
        // 在小螢幕上：根據 showSidebar 狀態決定顯示/隱藏
        // 在大螢幕上（lg以上）：始終顯示
        showSidebar ? 'translate-x-0' : '-translate-x-full lg:translate-x-0'
      ]"
    >
      <!-- 修改：頂部標題區域，顯示平台名稱 HoHoStay -->
      <div class="flex h-16 items-center gap-2 border-b border-border px-6 flex-shrink-0">
        <div class="flex h-10 w-10 items-center justify-center rounded-lg bg-gradient-to-br from-primary to-primary/80 shadow-lg flex-shrink-0">
          <Building2 class="h-6 w-6 text-primary-foreground" />
        </div>
        <div class="flex-1 min-w-0">
          <h1 class="text-lg font-bold text-foreground truncate">HoHoStay</h1>
          <p class="text-xs text-muted-foreground truncate">飯店管理平台</p>
        </div>
        <!-- 深色模式切換圖標按鈕 -->
        <Button 
          variant="ghost" 
          size="sm"
          class="h-8 w-8 p-0 flex-shrink-0" 
          @click="toggleDarkMode"
          :title="isDark ? '切換到淺色模式' : '切換到深色模式'"
        >
          <Moon v-if="!isDark" class="h-4 w-4" />
          <Sun v-else class="h-4 w-4" />
        </Button>
      </div>
      
      <!-- 導航區域（可滾動） -->
      <nav class="flex-1 space-y-1 p-4 overflow-y-auto">
        <!-- 主要模組導航 -->
        <div class="mb-4">
          <p class="mb-2 px-2 text-xs font-semibold text-muted-foreground uppercase tracking-wider">
            主要模組
          </p>
          
          <!-- 飯店管理 -->
          <Button 
            :variant="isHotelManagementPage ? 'secondary' : 'ghost'" 
            class="w-full justify-start relative mb-1" 
            @click="navigate('/')"
          >
            <!-- 活動指示線（左側） -->
            <div 
              v-if="isHotelManagementPage" 
              class="absolute left-0 top-1/2 -translate-y-1/2 h-8 w-1 rounded-r-full bg-primary"
            ></div>
            <Building2 class="mr-2 h-5 w-5" />
            飯店管理
          </Button>
        </div>

        <!-- 快速功能 -->
        <div class="mb-4">
          <p class="mb-2 px-2 text-xs font-semibold text-muted-foreground uppercase tracking-wider">
            快速功能
          </p>
          
          <!-- 新增飯店 -->
          <Button 
            :variant="isNewHotelPage ? 'secondary' : 'ghost'" 
            class="w-full justify-start relative mb-1" 
            @click="navigate('/hotels/new')"
          >
            <div 
              v-if="isNewHotelPage" 
              class="absolute left-0 top-1/2 -translate-y-1/2 h-8 w-1 rounded-r-full bg-primary"
            ></div>
            <Plus class="mr-2 h-5 w-5" />
            新增飯店
          </Button>
          
          <!-- 引導式新增 -->
          <Button 
            :variant="isHotelSetupPage ? 'secondary' : 'ghost'" 
            class="w-full justify-start relative mb-1" 
            @click="navigate('/hotels/setup')"
          >
            <div 
              v-if="isHotelSetupPage" 
              class="absolute left-0 top-1/2 -translate-y-1/2 h-8 w-1 rounded-r-full bg-primary"
            ></div>
            <ListChecks class="mr-2 h-5 w-5" />
            引導式新增
          </Button>
        </div>

        <!-- 當前飯店操作（當在特定飯店頁面時顯示） -->
        <div v-if="currentHotelId" class="mb-4">
          <p class="mb-2 px-2 text-xs font-semibold text-muted-foreground uppercase tracking-wider">
            當前飯店操作
          </p>
          
          <!-- 編輯飯店 -->
          <Button 
            :variant="isEditHotelPage ? 'secondary' : 'ghost'" 
            class="w-full justify-start relative mb-1" 
            @click="navigate(`/hotels/${currentHotelId}`)"
          >
            <div 
              v-if="isEditHotelPage" 
              class="absolute left-0 top-1/2 -translate-y-1/2 h-8 w-1 rounded-r-full bg-primary"
            ></div>
            <Settings class="mr-2 h-5 w-5" />
            編輯飯店
          </Button>

          <!-- 照片管理 -->
          <Button 
            :variant="isImagesPage ? 'secondary' : 'ghost'" 
            class="w-full justify-start relative mb-1" 
            @click="navigateToImages"
          >
            <div 
              v-if="isImagesPage" 
              class="absolute left-0 top-1/2 -translate-y-1/2 h-8 w-1 rounded-r-full bg-primary"
            ></div>
            <Image class="mr-2 h-5 w-5" />
            照片管理
          </Button>

          <!-- 房型管理 -->
          <Button 
            :variant="isRoomTypesPage ? 'secondary' : 'ghost'" 
            class="w-full justify-start relative mb-1" 
            @click="navigateToRoomTypes"
          >
            <div 
              v-if="isRoomTypesPage" 
              class="absolute left-0 top-1/2 -translate-y-1/2 h-8 w-1 rounded-r-full bg-primary"
            ></div>
            <Bed class="mr-2 h-5 w-5" />
            房型管理
          </Button>

          <!-- 設施管理 -->
          <Button 
            :variant="isFacilitiesPage ? 'secondary' : 'ghost'" 
            class="w-full justify-start relative mb-1" 
            @click="navigateToFacilities"
          >
            <div 
              v-if="isFacilitiesPage" 
              class="absolute left-0 top-1/2 -translate-y-1/2 h-8 w-1 rounded-r-full bg-primary"
            ></div>
            <Wrench class="mr-2 h-5 w-5" />
            設施管理
          </Button>
        </div>
      </nav>

      <!-- 修改：底部區域固定在側邊欄底部 - 與分頁欄高度一致 -->
      <div class="border-t border-border bg-background py-3 px-4 flex-shrink-0 flex flex-col justify-center">
        <!-- 修改：帳號名字加上房東圖標 -->
        <div class="mb-2 flex items-center gap-2 text-sm text-muted-foreground">
          <Crown class="h-4 w-4 text-primary" />
          <span class="truncate">{{ userEmail }}</span>
        </div>
        
        <!-- 返回平台首頁按鈕 -->
        <Button 
          variant="outline" 
          size="sm"
          class="mb-1.5 w-full h-8" 
          @click="goToUserHome"
        >
          <Home class="mr-2 h-4 w-4" />
          返回平台首頁
        </Button>
        
        <Button variant="outline" size="sm" class="w-full h-8" @click="handleLogout">
          <LogOut class="mr-2 h-4 w-4" />
          登出
        </Button>
      </div>
    </aside>

    <!-- 修改：主要內容區添加左邊距，避免被固定側邊欄遮擋 -->
    <!-- 在小螢幕上沒有左邊距，在大螢幕上有左邊距 -->
    <main class="flex-1 ml-0 lg:ml-64 overflow-auto">
      <!-- 手機版側邊欄切換按鈕（在小螢幕上顯示） -->
      <div class="lg:hidden fixed top-4 left-4 z-30">
        <Button
          variant="outline"
          size="sm"
          class="h-10 w-10 p-0 bg-background shadow-lg"
          @click="showSidebar = !showSidebar"
        >
          <Menu v-if="!showSidebar" class="h-5 w-5" />
          <X v-else class="h-5 w-5" />
        </Button>
      </div>

      <slot />
    </main>
  </div>
</template>

<script setup>
import { ref, onMounted, onUnmounted, computed } from 'vue'
import { useRouter, useRoute } from 'vue-router'
import { Building2, LogOut, Moon, Sun, Crown, Bed, Plus, Image, Settings, Wrench, ListChecks, Home, Menu, X } from 'lucide-vue-next'
import Button from '@/user/components/ui/Button.vue'
import { toast } from 'vue-sonner'
import { getTheme, toggleTheme, initTheme } from '@/user/utils/theme'
import { API_BASE_URL } from '@/user/api/request'

const router = useRouter()
const route = useRoute()
const userEmail = ref('')
const userId = ref(null)
const isDark = ref(false)
const hasAccess = ref(false) // 是否有權限訪問
const showSidebar = ref(false) // 控制側邊欄顯示狀態（僅用於小螢幕）

// 從路由中提取當前飯店 ID
const currentHotelId = computed(() => {
  // 優先從路由參數獲取（更可靠）
  if (route.params.id) {
    return route.params.id
  }
  if (route.params.hotelId) {
    return route.params.hotelId
  }
  // 如果參數不存在，從路徑中提取
  const path = route.path
  const match = path.match(/\/hotels\/(\d+)/)
  return match ? match[1] : null
})

// 檢查是否在飯店管理頁面（首頁或飯店列表）
const isHotelManagementPage = computed(() => {
  return route.path === '/'
})

// 檢查是否在新增飯店頁面
const isNewHotelPage = computed(() => {
  return route.path === '/hotels/new'
})

// 檢查是否在引導式新增頁面
const isHotelSetupPage = computed(() => {
  return route.path === '/hotels/setup' || route.path.includes('/hotels/') && route.path.includes('/setup')
})

// 檢查是否在編輯飯店頁面
const isEditHotelPage = computed(() => {
  return currentHotelId.value && route.path === `/hotels/${currentHotelId.value}`
})

// 檢查是否在照片管理頁面
const isImagesPage = computed(() => {
  return route.path.includes('/images')
})

// 檢查是否在房型管理頁面
const isRoomTypesPage = computed(() => {
  return route.path.includes('/room-types')
})

// 檢查是否在設施管理頁面
const isFacilitiesPage = computed(() => {
  return route.path.includes('/facilities')
})

//20251217 新增getHoUserFromStorage函數，統一從 storage 讀取 hoUser 完整資料的函數
const getHoUserFromStorage = (storage) => {
  //嘗試讀取Pinia的持久化數據（key:"hoUser"）
  const hoUserData = storage.getItem('hoUser')
  //如果讀取到資料，則解析為JSON格式
  if (hoUserData) {
    try {
      const parsed = JSON.parse(hoUserData)
      return parsed 
    } catch (error) {
      if (import.meta.env.DEV) {
        console.warn('解析 hoUser 數據失敗:', error)
      }
    }
  }
  //如果讀取不到資料，則返回null
  return null
}


// 檢查用戶角色是否為房東/業者（role_id = 3, HOTEL_OWNER）- 僅飯店管理頁面使用
const checkUserRole = () => {
  try { 
    // 優先從 sessionStorage 讀取，如果沒有則從 localStorage 讀取，如果都沒有則返回false
    const hoUser = getHoUserFromStorage(sessionStorage) || getHoUserFromStorage(localStorage)
    // 如果沒有 hoUser 或沒有角色資料，返回 false
  if (!hoUser?.role) {
      if (import.meta.env.DEV) {
        console.warn('未找到用戶角色資料，無法驗證是否為房東/業者身份')
      }
      return false
    }
    
    // 檢查是否有房東角色
    return hoUser.role.some(role => 
      role.id === 3 || role.name === '房東' || role.name === '業者' || role.name === 'HOTEL_OWNER'
    )
  } catch (error) {
    if (import.meta.env.DEV) {
      console.error('檢查用戶角色失敗:', error)
    }
    return false
  }
}

// 更新用戶資訊的函數（與 user 主頁同步）
// 清理用戶名稱，移除所有 "undefined" 字串
// 如果只有姓就顯示姓，如果只有名就顯示名，如果都有就顯示姓+名
const cleanUserName = (userName) => {
  if (!userName) return ''
  // 移除所有的 "undefined" 字串（不區分大小寫）
  const cleaned = userName.replace(/undefined/gi, '').trim()
  // 如果清理後為空，返回原值（避免完全清空）
  return cleaned || userName
}

const updateUserInfo = () => {
  const hoUser = getHoUserFromStorage(sessionStorage) || getHoUserFromStorage(localStorage)
  
  // userId
  userId.value = hoUser?.userId || hoUser?.id || null
  
  // userName（firstName + lastName）
  if (hoUser?.firstName || hoUser?.lastName) {
    const userName = `${hoUser.firstName || ''}${hoUser.lastName || ''}`
    userEmail.value = cleanUserName(userName) || 'user@example.com'
  } else {
    userEmail.value = 'user@example.com'
  }
  
  // 角色權限檢查
  hasAccess.value = hoUser?.role?.some(role => 
    role.id === 3 || role.name === '房東' || role.name === '業者' || role.name === 'HOTEL_OWNER'
  ) || false
}

// 監聽 localStorage 變化的函數
const handleStorageChange = (event) => {
  // 當 hoUser（Pinia 持久化數據）更新時，重新更新所有資訊
  if (event.key === 'hoUser' || event.key === null) {
    updateUserInfo()  // 只需要調用一次，它會更新所有資料（包括角色權限）
  }
}

// 監聽自定義的用戶角色更新事件
const handleUserRoleUpdated = (event) => {
  if (import.meta.env.DEV) {
    console.log('收到用戶角色更新事件:', event.detail)
  }
  // 直接更新用戶資訊（包括角色權限檢查）
  updateUserInfo()
}

onMounted(() => {
  // 初始化主題
  initTheme()
  isDark.value = getTheme() === 'dark'
  
  // 初始化用戶資訊（包含角色檢查）
  updateUserInfo()
  
  // 檢查是否有權限訪問（必須是房東/業者，ROLEID = 3）
  if (!hasAccess.value) {
    toast.error('您沒有權限訪問此頁面，僅限房東/業者使用')
    // 延遲重定向，讓用戶看到錯誤訊息
    setTimeout(() => {
      window.location.href = '/user.html'
    }, 2000)
    return
  }
  
  // 監聽用戶變更事件（開發模式切換房東時觸發）
  window.addEventListener('userChanged', updateUserInfo)
  
  // 監聽 localStorage 變化（與 user 主頁同步）
  window.addEventListener('storage', handleStorageChange)
  
  // 監聽用戶角色更新事件（註冊成為房東時觸發）
  window.addEventListener('userRoleUpdated', handleUserRoleUpdated)
})

// 組件卸載時移除事件監聽器
onUnmounted(() => {
  window.removeEventListener('userChanged', updateUserInfo)
  window.removeEventListener('storage', handleStorageChange)
  window.removeEventListener('userRoleUpdated', handleUserRoleUpdated)
})

const toggleDarkMode = () => {
  const newTheme = toggleTheme()
  isDark.value = newTheme === 'dark'
  toast.success(newTheme === 'dark' ? '已切換到深色模式' : '已切換到淺色模式')
}

// 登出並返回使用者登入畫面
const handleLogout = async () => {
  try {
    // 清除 localStorage 中的用戶數據
    localStorage.removeItem('token')
    localStorage.removeItem('userId')
    localStorage.removeItem('hoUser')
    localStorage.removeItem('userName')
    localStorage.removeItem('pinia-user')
    
    // 清除 sessionStorage
    sessionStorage.removeItem('token')
    sessionStorage.removeItem('userId')
    sessionStorage.removeItem('hoUser')
    sessionStorage.removeItem('adminToken')
    sessionStorage.removeItem('pinia-user')
    sessionStorage.removeItem('userName')
    
    // 調用後端登出 API（可選）
    try {
      await fetch(`${API_BASE_URL}/logout`, {
        method: 'GET',
        credentials: 'include'
      })
    } catch (error) {
      // 忽略登出 API 錯誤
      if (import.meta.env.DEV) {
        console.warn('登出 API 調用失敗:', error)
      }
    }
    
    toast.success('已登出')
    
    // 延遲一下讓用戶看到成功訊息，然後跳轉到使用者登入畫面（輸入帳密的畫面）
    setTimeout(() => {
      window.location.href = '/user.html/user/login'
    }, 500)
  } catch (error) {
    console.error('登出失敗:', error)
    toast.error('登出失敗，請稍後再試')
  }
}

const navigate = (path) => {
  router.push(path)
}

// 返回平台首頁（使用者首頁）
const goToUserHome = () => {
  window.location.href = '/user.html'
}

// 導航到照片管理（使用 id 參數）
const navigateToImages = () => {
  if (currentHotelId.value) {
    router.push(`/hotels/${currentHotelId.value}/images`)
  }
}

// 導航到房型管理（使用 hotelId 參數）
const navigateToRoomTypes = () => {
  if (currentHotelId.value) {
    router.push(`/hotels/${currentHotelId.value}/room-types`)
  }
}

// 導航到設施管理（使用 hotelId 參數）
const navigateToFacilities = () => {
  if (currentHotelId.value) {
    router.push(`/hotels/${currentHotelId.value}/facilities`)
  }
}
</script>

