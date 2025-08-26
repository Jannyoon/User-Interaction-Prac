<script lang="ts">
    let typing = $state(false)



</script>
<main>
<div class="popup" class:active={typing}>
    
</div>
  <div class="container">
    <div class="focusable" tabindex="0">첫 번째 항목</div>
    <div class="focusable" tabindex="0">두 번째 항목</div>
    <div class="focusable" tabindex="0">세 번째 항목</div>
  </div>
  <form>
    <label for="name">이름:</label>
    <input id="name" type="text" placeholder="이름을 입력하세요" class="focusable" />

    <label for="message">메시지:</label>
    <textarea id="message" placeholder="메시지를 입력하세요" class="focusable"></textarea>

    <button type="submit" class="focusable">전송</button>
  </form>
</main>

<style>

    .popup {
        width: 300px;
        height:300px;
        background-color: yellow;
        position: absolute;
        top: 0;
        left:0;
    }
  .container {
    display: flex;
    flex-direction: column;
    gap: 10px;
    margin-bottom: 20px;
  }

  .focusable {
    padding: 8px;
    border: 1px solid #ccc;
    border-radius: 4px;
    outline: none;
  }

  .focusable:focus {
    border-color: #007bff;
  }

  form {
    display: grid;
    gap: 10px;
  }

  label {
    font-weight: bold;
  }

  input,
  textarea {
    padding: 8px;
    font-size: 1rem;
    border: 1px solid #ccc;
    border-radius: 4px;
  }

  button {
    padding: 10px;
    background-color: #007bff;
    color: white;
    border: none;
    border-radius: 4px;
    cursor: pointer;
  }

  button:hover {
    background-color: #0056b3;
  }
</style>