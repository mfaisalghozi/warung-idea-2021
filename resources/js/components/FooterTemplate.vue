<template>
  <div class="footer-wrapper">
    <div class="text-question">
      <div>
        Have a question about our app ?
      </div>
      let us help you!
    </div>
    <div class="field-email">
      <form action="mailto:mfaisalghozi99@gmail.com" method="post" enctype="text/plain">
        <input type="text" name="message" placeholder="your question"/>
        <button type="submit">Send Feedback</button>
      </form>
    </div>
    <div class="info-app">
      <div class="logo-image">
        <img src="\images\icon\light-bulb.png" alt="bright-lamp">
        <div class="text-image">WarungIdea.com</div>
      </div>
      <div class="love-text">
        <a href="https://github.com/mfaisalghozi/warung-ide-2021" target="blank">
          Made with 🖤 from us
        </a>
      </div>
      <div class="logo-socmed">
        <a href="#twitter">
          <img src="\images\icon\twitter.svg" alt="twitter-icon">
        </a>
        <a href="#instagram">
          <img src="\images\icon\instagram.svg" alt="instagram-icon">
        </a>
        <a href="#facebook">
          <img src="\images\icon\facebook.svg" alt="facebook-icon">
        </a>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: "FooterTemplate",
}
</script>

<style lang="less" scoped>
.footer-wrapper {
  background-color: rgba(0,0,0,.01);
  border-top: 1px solid rgba(0,0,0,.1);
  display: flex;
  flex-direction: column;
  height: 25rem;
  align-items: center;
  justify-content: space-around;

  .text-question {
    font-family: 'Inter', sans-serif;
    font-family: 'Zen Antique Soft', serif;
    font-size: 48px;
    font-style: italic;
    width: 40%;
  }

  .field-email {
    margin: 2rem 0;
    input {
      width: 300px;
      height: 46px;
      margin-right: 1rem;
      border-radius: 20px;
      text-align: center;
    }
    button {
      margin-left: 1rem;
      border-radius: 10px;
      width: 136px;
      height: 48px;

      &:hover {
        background-color: #55D8C1;
        color: white;
      }
    }
  }

  .info-app {
    display: flex;
    flex-direction: row;
    width: 100%;
    justify-content: space-between;

    .love-text > a {
      text-decoration: none;
      color: black;

      &:hover {
        color: #4FE3CA;
      }
    }

    .logo-image {
      margin-left: 3rem;
      display: flex;
      flex-direction: row;
      align-items: center;
      justify-content: space-around;

      .text-image {
        margin-left: .7rem;
        font-size: 1.2rem;
        font-family: 'Zen Antique Soft', serif;
      }

      img {
        height: 34px;
        width: 28px;
      }
    }

    .logo-socmed {
      display: flex;
      flex-direction: row;
      justify-content: space-around;
      margin-right: 3rem;
      width: 10%;
    }
  }
}
</style>
