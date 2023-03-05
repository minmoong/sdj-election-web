<script>
  import { fly } from 'svelte/transition';
  export let candidates;
  export let startVote;
  
  let showModal = false;
  let showEndModal = false;
  let num, name, pname;
  let isDuplicatedVotes = false;
  let currentIdx = 0;

  function addCandidate() {
    candidates = [...candidates, {num, name, pname, votes: 0, avatar}];
    showModal = false;
    num = '';
    name = '';
    pname = '';
    avatar = null;
  }

  function endVote() {
    candidates.sort((a, b) => a.votes > b.votes ? -1 : 1);

    // 중복투표 확인
    if (candidates[0].votes == candidates[1].votes) {
      isDuplicatedVotes = true;
    }

    showEndModal = true;
  }

  function nextCandidate() {
    currentIdx = currentIdx + 1;
  }

  function prevCandidate() {
    currentIdx = currentIdx - 1;
  }

  let avatar, fileinput;

	function onFileSelected(e) {
    let image = e.target.files[0];
    let reader = new FileReader();
    reader.readAsDataURL(image);
    reader.onload = e => {
      avatar = e.target.result
    };
  }
</script>

<div class="screen">
  {#if showModal}
    <div class="backdrop" >
      <div class="add-modal" in:fly={{ y: -200, duration: 600 }}>
        <div class="modal-title">후보자 정보 입력</div>
        {#if avatar}
          <img class="avatar" src={avatar} alt="candidate poster" width="200px" />
          {:else}
            <div class="loadImage-label">
              <label for="loadImage">
                <div>
                  <img src="images/add_photo.png" alt="add icon" style="opacity:0.5" />
                </div>
                <div>후보자 선거 포스터 업로드</div>
              </label>
            </div>
        {/if}
        <input type="file" id="loadImage" accept=".jpg, .jpeg, .png" on:change={(e) => onFileSelected(e)} bind:this={fileinput} />
        <input type="text" placeholder="후보자 기호(숫자만 입력)" bind:value={num} />
        <input type="text" placeholder="후보자 성함" bind:value={name} />
        <input type="text" placeholder="후보자 정당 이름" bind:value={pname} />
        <!-- <input type="text" placeholder="후보자 캐치프레이즈" bind:value={cphrase} /> -->
        <button class="btn-cancel" on:click={() => {showModal = false; }}>취소</button>
        <button class="btn-add" on:click={addCandidate}>등록</button>
      </div>
    </div>
  {/if}
  {#if showEndModal}
    <div class="backdrop">
      <div class="add-modal result" in:fly={{ y: -200, duration: 600 }}>
        <div class="modal-title">선거 결과</div>
        {#if !isDuplicatedVotes}
          <div class="modal-content">
            {#if currentIdx == 0}
              <div class="result-message">🎉 당선을 축하드립니다! 🎉</div>
            {/if}
            <div class="result-info">
              <img src={candidates[currentIdx].avatar ? candidates[currentIdx].avatar : 'images/candidate_image.png'} alt="당선자 포스터 사진" width="230px" />
              <div class="winner-info">
                <div class="winner-name">기호 {candidates[currentIdx].num}번 후보자 {candidates[currentIdx].name}</div>
                <div class="winner-pname">정당: {candidates[currentIdx].pname}</div>
                <div class="winner-votes">득표수: {candidates[currentIdx].votes}</div>
                <div class="next-prev-control">
                  {#if currentIdx == 0}
                    <button class="btn-next-candidate" on:click={nextCandidate}>다음 후보자 →</button>
                    {:else if currentIdx > 0 && currentIdx < candidates.length - 1}
                    <button class="btn-prev-candidate" on:click={prevCandidate}>← 이전</button>
                      <button class="btn-next-candidate" on:click={nextCandidate}>다음 →</button>
                    {:else}
                      <button class="btn-prev-candidate" on:click={prevCandidate}>← 이전 후보자</button>
                  {/if}
                </div>
              </div>
            </div>
          </div>
          {:else}
            <div class="result-message">당선자가 중복되었습니다.</div>
        {/if}
      </div>
    </div>
  {/if}
  {#if !startVote}
    <button class="btn-candidate" on:click={() => { showModal = true; }}>+ 후보자 등록</button>
    <button class="btn-election-start" on:click={() => { startVote = true; }}>투표 시작</button>
    {:else}
      <button class="btn-election-end" on:click={endVote}>투표 마감</button>
  {/if}
</div>

<style>
  button {
    padding: 10px 20px;
    border-radius: 20px;
    cursor: pointer;
    font-size: 20px;
    border: 2px solid #275efe;
  }
  
  .btn-candidate {
    color: #275efe;
    margin-right: 20px;
  }

  .btn-election-start {
    background-color: #275efe;
    color: #fff;
  }

  .btn-election-end {
    background-color: #dc3545;
    border: 2px solid #dc3545;
    color: #fff;
  }

  .btn-cancel {
    padding: 8px 16px;
    color: #275efe;
    margin-right: 10px;
  }

  .btn-add {
    padding: 8px 16px;
    background-color: #275efe;
    color: #fff;
  }

  .backdrop {
    width: 100%;
    height: 100%;
    position: fixed;
    top: 0;
    left: 0;
    background: rgba(0, 0, 0, 0.4);
  }

  .add-modal {
    padding: 15px;
    border-radius: 10px;
    max-width: 400px;
    margin: 10% auto;
    text-align: center;
    background: white;
  }

  .add-modal .modal-title {
    font-size: 20px;
    font-weight: bold;
    margin-bottom: 20px;
  }

  #loadImage {
    visibility: hidden;
  }

  .loadImage-label {
    display: block;
    text-align: center;
    border: 3px dashed #7f7f7f;
    padding: 7px 0;
    margin: 0 20px;
  }
  
  .loadImage-label label {
    cursor: pointer;
    font-size: 20px;
    color: #7f7f7f;
  }

  .add-modal input:not(#loadImage) {
    display: block;
    font-size: 17px;
    width: 90%;
    padding: 5px 10px;
    box-sizing: border-box;
    border: 2px solid #d3d3d3;
    outline: none;
    text-indent: 10px;
    border-radius: 30px;
    margin: 0 auto 15px;
  }

  .add-modal input:focus:not(#loadImage) {
    border: 2px solid #275efe;
  }

  .add-modal.result {
    max-width: 600px;
    padding-bottom: 30px;
  }

  .add-modal.result .result-message {
    font-size: 28px;
    color: #275efe;
    font-weight: bold;
    margin-bottom: 20px;
  }

  .result-info {
    display: flex;
    justify-content:center;
  }

  .result-info img {
    margin-right: 40px;
    border-radius: 10px;
  }

  .winner-info {
    position: relative;
    font-size: 25px;
  }

  .winner-name {
    font-size: 28px;
  }

  .next-prev-control {
    position: absolute;
    left: 0;
    right: 0;
    bottom: 0;
    display: flex;
    justify-content: center;
  }

  .next-prev-control button {
    color: #275efe;
  }

  .btn-prev-candidate {
    margin-right: 10px;
  }
</style>