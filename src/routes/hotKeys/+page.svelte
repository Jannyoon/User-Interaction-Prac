<script lang="ts">
	import { onMount } from "svelte";

    let itemRef:HTMLInputElement;
    let nameRef:HTMLInputElement;
    let messageRef:HTMLTextAreaElement;

    let typing = $state(false)
    let activeIdx = $state([0,0]) //row, col
    let activeVal = $state('')

    let defaultVal = '출발지'
    let cardRow = $state([['test1', 'test2', 'test3'], ['test4', 'test5']])
    let fieldLists = $state(['test1', 'test2', 'test3'])

    let activeRowLastIdx = $derived(cardRow.length>0 ? cardRow[activeIdx[0]].length-1 : -1)

    function handleDeleteItem(rowidx: number, colidx: number){
      cardRow[rowidx].splice(colidx, 1);
    }

    function handleAddItem(addRow: number){
      cardRow[addRow].push(cardRow[addRow][0])
    }


    // onMount(()=>{
    //   const first = document.querySelectorAll('.container')[activeIdx[0]].querySelectorAll<HTMLElement>('.focusable')[0]
    //   first.focus()
    // })

    $effect(()=>{
      console.log("activeVal", activeVal)
    })

    $effect(()=>{
      // const handleFocusTab = (e:FocusEvent)=>{
      //   const active = document.activeElement
      //   // console.log("현재 활성화 된 곳", active)
        
      //   const focusedInputElement = active?.querySelector('input')
      //   // console.log("input 출력", focusedInputElement)
      //   focusedInputElement?.select()
  
      // }

      const addThroughTab = (e:KeyboardEvent) => {
        //이전 state 참조
        const rowIdx = activeIdx[0]
        const colIdx = activeIdx[1]
        const lastIdx = activeRowLastIdx

        if (e.key==='Tab' && e.shiftKey){
          console.log("ㅇㅇ") 
          console.log("현재 activeIdx", activeIdx)
          activeIdx = [rowIdx, colIdx-1]
          if (colIdx===0){
            if (activeIdx[1]<0) e.preventDefault() //실시간 참조
            // rowIdx 번째 container 안의 .dateTime 안에 있는 .time 선택
            const container = document.querySelectorAll('.container')[rowIdx];
            const timeDiv = container.querySelector<HTMLElement>('.dateTime .time');
            timeDiv?.focus();
          } else if (colIdx>0){
            const prev = document.querySelectorAll('.container')[rowIdx].querySelectorAll<HTMLElement>('.focusable')[colIdx-1]
            prev?.focus()}
          // } else {
          //   console.log("activeIdx", activeIdx)
          //   const prev = document.querySelectorAll('.container')[rowIdx].querySelector('.container')
          //   console.log(prev)
          // }
        }
        else if (e.key==='Tab' && colIdx===lastIdx && !e.shiftKey) cardRow[rowIdx].push(cardRow[rowIdx][0])
      }      
      document.addEventListener('keydown', addThroughTab)
      // document.addEventListener('focusin', handleFocusTab)

      return ()=>{
        document.removeEventListener('keydown', addThroughTab)
        // document.removeEventListener('focusin', handleFocusTab)
      }      
    })

</script>
<main>
  <div class="card">
    {#each cardRow as row, rowIdx}
    <div class="container">
      <div class="dateTime">
        <div class="date" tabindex="0" autofocus={rowIdx===0} 
        
          onfocus={()=>{
            activeVal=""
            activeIdx = [rowIdx, -2]
          }}          
          onclick={(e:MouseEvent)=>{
            activeVal = ""
            activeIdx = [rowIdx,-2]

            const target = e.currentTarget as HTMLElement
            console.log(target, '클릭 타겟')
            target.focus()
          }}>
          <p>Date</p>
          <p>ㅌㅌ</p>
        </div>
        <div class="time" tabindex="0" 
          onfocus={()=>{
            activeVal=""
            activeIdx = [rowIdx, -1]
          }}  
          onclick={(e:MouseEvent)=>{
            activeVal = ""
            activeIdx = [rowIdx,-1]

            const target = e.currentTarget as HTMLElement
            console.log(target, '클릭 타겟')
            target.focus()}}
          >
          <p>Time</p>
          <p>00:00</p>
        </div>
      </div>
      {#each row as item, colIdx}
        <div class="focusable" tabindex="0" 
          onclick={()=>{
            activeVal = item
            activeIdx = [rowIdx, colIdx]
          }}
          onfocus={(e: FocusEvent)=>{
            activeVal = item
            activeIdx = [rowIdx, colIdx]

            const parent = e.currentTarget as HTMLElement
            const input = parent.querySelector('input') as HTMLInputElement | null
            input?.select()
          }}>
          <input id="field" type="text" tabindex="-1"  
          placeholder="추가할 항목을 입력하세요." 
          class="addField" 
          bind:this={itemRef} 
          bind:value={cardRow[rowIdx][colIdx]} 
          /> <!-- Proxy 객체 - 중첩 객체는 참조하지 않음-->
          <button class="deleteBtn" tabindex="-1"  onclick={()=>handleDeleteItem(rowIdx, colIdx)}>x</button>
        </div>
      {/each}
      <!-- <input id="field" type="text" placeholder="추가할 항목을 입력하세요." class="addField" bind:this={itemRef}/> -->
       {#if rowIdx===0 || cardRow[rowIdx]}
      <div class="focusable addBtn">
        <button onclick={() => handleAddItem(rowIdx)}>항목 추가하기</button>
      </div>    
      {/if}
    </div>
    {/each}
    <div class="addDate"><button class="addDateBtn">이용일 추가</button></div>
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
      display: none;
      width: 300px;
      height:300px;
      background-color: yellow;
      position: absolute;
      top: 0;
      left:0;

      &.active {
          display: block;
      }
  }

  .card {
    background-color: beige;
    padding: 2rem 1rem 2rem 1rem;
  }


  .container {
    display: flex;
    flex-wrap: wrap;
    gap: 10px;
  }

  .dateTime {
    width: 20rem;
    margin-bottom: 0.2rem;

    background-color: white;
    border: 1px solid #ccc;
    border-radius: 4px;
    outline: none;
    
    display: flex;
  }

  .date {
    width: 60%;
    height : 100%;
    border-right : 1px solid #ccc;    

    padding-left: 1rem;
    padding-right: 1rem;
  }

  .time {
    width: 40%;
    padding-left: 1rem;
    padding-right: 1rem;

  }

  .date:focus-within {
    border: 1px solid #007bff;
    border-color: #007bff;
  }

  .time:focus-within {
    border: 1px solid #007bff;  
    border-color: #007bff;
  }

  .focusable {
    width: 10rem;
    margin-bottom: 0.2rem;
    padding-top: 0.5rem;
    padding-bottom: 0.5rem;
    padding-left: 1rem;
    padding-right: 1rem;

    background-color: white;
    border: 1px solid #ccc;
    border-radius: 4px;
    outline: none;

    display: flex;
    align-items: center;
    justify-content: space-between;
  }

  .focusable:focus-within {
    border-color: #007bff;
  }

  .addBtn {
    background-color: rgb(238, 238, 238);
    border: 3px dotted #ccc;
  }

  .addDate {
    margin-top : 1rem;
    width: 6rem;
    padding : 1rem 2rem 1rem 2rem;
    text-align: center;

    border-radius : 10px;
    background-color: navy;
    color: white;
    font-size: small;
  }

  .addDateBtn {
    width: 100%;
    background-color: transparent;
    color: white;
  }


  form {
    margin-top : 5rem;
    display: grid;
    gap: 10px;
  }

  label {
    font-weight: bold;
  }

  input {
    padding: 8px;
    width: 100%;
    text-align: center;
    border: none;
    background-color: transparent;
  }

  input:focus {
    outline: none;
  }

  textarea {
    padding: 8px;
    font-size: 1rem;
    border: 1px solid #ccc;
    border-radius: 4px;
  }

  button {
    width: 100%;
    padding: 10px;
    background-color: transparent;
    border: none;
    border-radius: 4px;
    cursor: pointer;

    &.deleteBtn {
     background-color: tomato;
     width: 30px;
     height: 30px;
     font-size: 1.1rem;
     display: flex;
     align-items: center;
     justify-content: center;
     border-radius: 100%;
    }
  }

  button:focus {
    outline: none;
  }

</style>