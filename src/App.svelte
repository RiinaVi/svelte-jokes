<script lang="ts">
  import { onMount } from 'svelte'

  let data;
  let isLoading = false;
  let inputValue;
  let categories = [];
  let category;
  let radioValue = 1;
  const generateJoke = async () => {
    isLoading = true;

    await fetch(`https://matchilling-chuck-norris-jokes-v1.p.rapidapi.com/jokes/random`, {
      method: 'get',
      headers: {
        'content-type': 'application/json',
        "x-rapidapi-key": "211173553bmsh4fda991125e9bcap1b8f69jsn56e13979e687",
      },
    })
        .then(x => x.json())
        .then((x) => {
          JSON.stringify(x)
          data = x
          // console.log(x);
          isLoading = false;
        })
  }

  onMount(async () => {
    await generateJoke();
    await getCategories();
  })

  const jokesSearch = async () => {
    isLoading = true;
    await fetch(`https://api.chucknorris.io/jokes/search?query=${inputValue}${category ? '&category=' + category : ''}`, {
      method: 'get',
      headers: {
        'content-type': 'application/json',
        "x-rapidapi-key": "211173553bmsh4fda991125e9bcap1b8f69jsn56e13979e687",
      },
    })
        .then(x => x.json())
        .then((x) => {
          JSON.stringify(x)
          data = x.result[Math.floor(Math.random() * x.result.length)]
          isLoading = false;
        })
  }


  const getCategories = async () => {
    isLoading = true;
    await fetch(`https://api.chucknorris.io/jokes/categories`, {
      method: 'get',
      headers: {
        'content-type': 'application/json',
        "x-rapidapi-key": "211173553bmsh4fda991125e9bcap1b8f69jsn56e13979e687",
      },
    })
        .then(x => x.json())
        .then((x) => {
          JSON.stringify(x)
          categories = x
          isLoading = false;
        })
  }

  const searchByCategory = async () => {
    isLoading = true;
    await fetch(`https://api.chucknorris.io/jokes/random?category=${category ? category : 'animal'}`, {
      method: 'get',
      headers: {
        'content-type': 'application/json',
        "x-rapidapi-key": "211173553bmsh4fda991125e9bcap1b8f69jsn56e13979e687",
      },
    })
        .then(x => x.json())
        .then((x) => {
          JSON.stringify(x)
          data = x
          isLoading = false;
        })
  }

  const getJoke = async () => {
    if (radioValue === 2) {
     await searchByCategory();
      return;
    }
    if (radioValue === 3) {
     await jokesSearch();
      return;
    }
    await generateJoke();
  }
</script>

<main>
    <h1>Hey</h1>
    <h3>Let’s try to find a joke for you:</h3>
    <div id="radioGroup">
        <div class="radioContainer">
            <input type="radio" id="randomRadio"  bind:group={radioValue} value={1}>
            <label for="randomRadio">Random</label>
        </div>
        <div class="radioContainer">
            <input type="radio" id="categoryRadio" bind:group={radioValue} value={2}>
            <label for="categoryRadio">From Categories</label>
        </div>

        {#if radioValue === 2}
            <select name="categories" id="categories" bind:value={category}>
                {#each categories as category}
                    <option value={category}>
                        {category}
                    </option>
                {/each}
            </select>
        {/if}
        <div class="radioContainer">
            <input type="radio" id="searchRadio" bind:group={radioValue} value={3}>
            <label for="searchRadio">Search</label>
        </div>
        {#if radioValue === 3}
            <input id="jokesSearch" placeholder="Free text search..." name="jokesSearch" type="text" bind:value={inputValue}/>
        {/if}
    </div>
    <button class="animatedButton" type="button" on:click={getJoke}>
        Get a joke
    </button>
    {#if isLoading}
        <p>Loading...</p>
    {:else }
        {#if data}
            <div class="jokeCard">
                {data.value}
                {#if data.categories.length}
                    <div class="jokeCategory">
                        {#each data.categories as category}
                            {category}
                        {/each}
                    </div>
                {/if}
            </div>
        {/if}


    {/if}
</main>

<style>
    main {
        text-align: center;
        padding: 1em;
        max-width: 240px;
        margin: 0 auto;
        height: 60vh;
        display: flex;
        flex-direction: column;
        align-items: center;
        justify-content: space-between;
    }

    @media (min-width: 640px) {
        main {
            max-width: none;
        }
    }

    .animatedButton {
        background: linear-gradient(90.55deg, #8EA7FF 0%, #7291FF 100%);
        border-radius: 10px;
        position: relative;
        width: 152px;
        height: 42px;
        /*left: 140px;*/
        top: 20px;
        bottom: 20px;
        font-weight: bold;
        font-size: 16px;
        line-height: 0;
        color: #FFFFFF;
        cursor: pointer;
        padding: 20px 30px;
        margin-bottom: 50px;
    }

    .jokeCard {
        position: relative;
        max-width: 680px;
        height: 225px;
        padding: 40px;
        margin-bottom: 20px;
        background: #F8F8F8;
        border-radius: 20px;
    }

    .jokeCategory {
        width: 107px;
        height: 24px;
        background: #FFFFFF;
        border-radius: 6px;
        right: 20px;
        bottom: 15px;
        position: absolute;
        letter-spacing: 2px;
        text-transform: uppercase;
        font-weight: 500;
        font-size: 10px;
        line-height: 20px;
        color: #333333;
    }

    .radioContainer {
        display: flex;
    }

    input[type='radio'] {
        margin-top: 2px;
        margin-right: 5px;
    }
</style>
