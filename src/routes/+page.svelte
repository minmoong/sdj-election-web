<script>
  import Screen from '../components/screen.svelte';
  import MP_Blop from '/src/audio/MP_Blop.mp3';

  let numberUnits = ['','만','억','조','경','해','자','양','구','간','정','재','극'];
  let candidates = [];
  let startVote = false;

  function addVote(i) {
    new Audio(MP_Blop).play();
    candidates[i].votes = candidates[i].votes + 1;
  }
</script>

<div class="mobile-ui">현재 웹 UI가 모바일 규격의 화면 크기에 최적화 되어있지 않습니다. 데스크탑으로 접속해주세요.</div>
<div class="container">
  <div class="screen" class:startvote={startVote}>
    <Screen bind:candidates bind:startVote />
  </div>
  <div class="candidate-container">
    {#each candidates as {num, name, pname, votes}, i}
      <div class="candidate">
        <div class="votes">득표수: {votes}</div>
        <div class="pname">{pname}</div>
        <img src={votes < 10 ? `images/${votes+1}.png` : "images/10.png"} alt="building" width="{140 + 7*votes}px" height="{140 + 7*votes}px"/>
        <div class="desc">
          기호 {num}번 후보자<br/>{pname} 대표 {name}<br />자산 {votes*100 + (votes < 13 ? `${numberUnits[votes]}` : 'WOW')}원
        </div>
        {#if startVote}
          <!-- svelte-ignore a11y-click-events-have-key-events -->
          <div class="add-vote" on:click={() => { addVote(i); }}>투표 +1</div>
          <!-- <div class="cancel-vote" on:click={() => { cancelVote(num); }}>투표 취소 -1</div> -->
        {/if}
      </div>
    {/each}
  </div>
</div>

<style>
  .mobile-ui {
    position: absolute;
    top: 50%;
    left: 50%;
    transform: translate(-50%, -50%);
  }
  
  @media screen and (max-width: 600px) {
    :not(.mobile-ui) {
      visibility: hidden;
    }
  }

  .container {
    width: 100%;
    height: 100%;
    background: url('https://user-images.githubusercontent.com/62737839/222896344-b2616b1a-ad81-4ddc-b623-12426f0468ce.jpg') no-repeat;
    background-size: cover;
    backdrop-filter: blur(10px);
  }

  .screen {
    display: flex;
    justify-content: center;
    align-items: center;
    min-height: 40vh;
  }

  .screen.startvote {
    min-height: 30vh;
  }

  .candidate-container {
    margin: 0 auto;
    max-width: 55%;
    display: flex;
    justify-content: space-between;
  }

  .candidate {
    text-align: center;
  }

  .candidate .pname {
    text-align: center;
    font-size: 40px;
  }

  .candidate .desc {
    font-size: 30px;
  }

  .candidate .add-vote {
    width: 70px;
    height: 30px;
    padding: 10px 15px;
    border-radius: 50px;
    font-size: 20px;
    border: 2px solid #275efe;
    color: #275efe;
    cursor: pointer;
    line-height: 30px;
    margin: 10px auto 0;
  }

  .candidate .votes {
    font-size: 25px;
  }

  /* .candidate .cancel-vote {
    width: 60px;
    height: 30px;
    padding: 5px 7px;
    border-radius: 15px;
    font-size: 17px;
    background: red;
    color: #fff;
    cursor: pointer;
    line-height: 15px;
    margin: 5px auto 0;
  } */
</style>