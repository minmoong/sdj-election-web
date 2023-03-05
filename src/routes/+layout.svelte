<div class="root">
  <slot />
  <div class="madeby">Made by 10603 김민규</div>
</div>

<style>
  .root {
    width: 100%;
    height: 100%;
  }

  .madeby {
    position: absolute;
    bottom: 20px;
    right: 20px;
    color: #275efe;
    font-size: 20px;
    opacity: 0.7;
  }
</style>