<script lang="ts">
  export let data: {
    profileData: { xp: number; level: number } | null,
    userXpData: { level: number; nextRequiredXp: number; cumulativeXp: number }[]
  };

  import { goto } from '$app/navigation';
  import { t } from 'svelte-i18n';

  // 사용자 프로필 정보 변수들
  let userLevel = 1;
  let userXp = 0;
  let baseXp = 0;
  let levelRequirement = 0;
  let currentXpProgress = 0;

  type UserXpEntry = {
    level: number;
    nextRequiredXp: number;
    cumulativeXp: number;
  };

  if (data.profileData) {
    userLevel = data.profileData.level;
    userXp = data.profileData.xp;
    
    // userXpData 배열에서 현재 레벨의 데이터 찾기 (현재 레벨의 cumulativeXp를 기준으로 함)
    const currentLevelData = data.userXpData.find((item: UserXpEntry) => item.level === userLevel);
    if (currentLevelData) {
      // 레벨이 1보다 크면 이전 레벨의 누적 xp가 baseXp가 됨 (없으면 0)
      if (userLevel > 1) {
        const previousLevelData = data.userXpData.find((item: UserXpEntry) => item.level === userLevel - 1);
        baseXp = previousLevelData ? previousLevelData.cumulativeXp : 0;
      } else {
        baseXp = 0;
      }
      // 현재 레벨에서 다음 레벨까지 필요한 xp는 현재 레벨의 누적 xp에서 baseXp를 뺀 값
      levelRequirement = currentLevelData.cumulativeXp - baseXp;
      // 현재 레벨에서 획득한 xp는 전체 누적 xp에서 baseXp를 차감한 값
      currentXpProgress = userXp - baseXp;
    }
  }
</script>

<div class="profile-container">
  <h1>{$t('profile')}</h1>
  <p>{$t('level')}: {userLevel}</p>
  
  <div class="xp-bar-container">
    <div class="progress-wrapper">
      <progress value={currentXpProgress} max={levelRequirement}></progress>
      <span class="progress-text">
        {$t('xpLabel')} {currentXpProgress} / {levelRequirement}
      </span>
    </div>
    <p class="total-xp">{$t('totalXp')}: {userXp}</p>
  </div>
</div>

<!-- 뒤로가기 버튼 -->
<button class="back-btn" on:click={() => goto('/')}>{$t('backButton')}</button>

<style>
  .profile-container {
    max-width: 600px;
    margin: 2rem auto;
    padding: 1rem;
    text-align: center;
  }
  
  h1 {
    font-size: 2rem;
    margin-bottom: 1rem;
  }
  
  p {
    font-size: 1.1rem;
    margin: 0.5rem 0;
  }
  
  .xp-bar-container {
    margin: 2rem 0;
    text-align: center;
  }
  
  .progress-wrapper {
    position: relative;
    width: 100%;
  }
  
  progress {
    width: 100%;
    height: 25px;
    -webkit-appearance: none;
    appearance: none;
  }
  
  progress::-webkit-progress-bar {
    background-color: #eee;
    border-radius: 5px;
  }
  
  progress::-webkit-progress-value {
    background-color: #B7DDBF;
    border-radius: 5px;
  }
  
  .progress-text {
    position: absolute;
    top: 50%;
    left: 50%;
    transform: translate(-50%, -50%);
    font-weight: bold;
    color: #000;
  }
  
  .total-xp {
    color: #666;
    font-size: 0.95rem;
    margin-top: 0.5rem;
  }
  
  .back-btn {
    position: fixed;
    bottom: 20px;
    left: 20px;
    background: #D7D4CD;
    color: #000;
    padding: 0.5rem 1rem;
    border: 1px solid #DDDDDD;
    border-radius: 4px;
    z-index: 1000;
    margin: 0;
    cursor: pointer;
  }
</style>
