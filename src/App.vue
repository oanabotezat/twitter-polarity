<template>
  <div id="app">
    <img src="./assets/pineview-logo.svg" width="100">
    <img src="./assets/logo.png" width="100">
    <p>Microsoft have released a bunch of new APIs for their <a
      href="https://azure.microsoft.com/en-us/try/cognitive-services/">Azure Cognitive Services</a>. One of them
      (Language API) can analyse some text and establish the sentiment (positive, negative, neutral).</p>
    <p>Use the API to build something which tells if a tweet mentioning Brexit (#brexit) is positive, negative or
      neutral. Access the tweet any way you want (copy a tweet from twitter manually and paste into an input file if you
      like!). Take as many shortcuts as you like but at least don’t skip using the APIs.</p>

    <p>AZURE API reference: <a
      href="https://docs.microsoft.com/en-us/azure/cognitive-services/text-analytics/quickstarts/nodejs">https://docs.microsoft.com/en-us/azure/cognitive-services/text-analytics/quickstarts/nodejs</a>
    </p>
      <h2>find english language tweets <a onclick="window.open('https://twitter.com/hashtag/brexit?lang=en', '_blank')">here</a></h2>

    <textarea v-model="tweet" placeholder="paste an enghlish language tweet please"></textarea>

    <h1>#brexit tweet:</h1>

    <blockquote :class="polarity">{{ tweet }}</blockquote>

    <h3 :class="polarity">
      polarity: {{polarity}}
    </h3>

  </div>
</template>


<script>


  export default {

    name: 'app',
    data() {
      return {
        tweet: "If you are tired of listening to politicians about #Brexit , listen instead to Ian Wright  , CEO of @Foodanddrinkfed as quoted in today’s @ScotNational :   “The consequences of a no-deal Brexit for UK food and drink are starting to be felt already. The impacts will snowball,”",
        tweet: "What I love best about the chaos amongst our noisy political elite is that it was brought about by ‘the little people’ - 17.4million of us, sick of not being heard. #Standstrong for #Brexit ",
        tweet: "New study is out on the flow of banking & financial services jobs from London to Frankfurt (as a consequence of #Brexit). Compiled by @Helaba, the regional state bank of Hesse & Thuringia.\n" +
        "\n" +
        "Report is in German, but I translated key parts into English. (See thread below)",
        // tweet: "1) Thread - Leavers \"bored with the details\" on #Brexit\n" +
        // "Fascinating poll which reveals 66% of Leave Voters are uninterested in exactly how we leave the EU, they just want to leave.  This brings into question - exactly *what* are we achieving pushing for Brexit?"
      }
    },

    asyncComputed: {
      polarity: function() {

        let accessKey = '1c6d760647e649f99dce93da6a960148';
        let uri = 'westcentralus.api.cognitive.microsoft.com';
        let path = '/text/analytics/v2.0/';

        let documents = {
          "documents": [
            {
              "language": "en",
              "id": "1",
              "text": this.tweet
            }
          ]
        }

        let body = JSON.stringify(documents);

        let sentiment =  this.$http.post(`https://${uri}${path}sentiment`, body, {
          headers: {
            'Ocp-Apim-Subscription-Key': accessKey,
            'Content-Type': 'text/json'
          }
        })
          .then(function(response) {
            // Success

            if (response.data && response.data.documents && response.data.documents[0] && response.data.documents[0].id === '1') {

              if (response.data.documents[0].score > 0.5) {

                return 'positive';
              }

              if (response.data.documents[0].score < 0.5) {

                return 'negative';
              }

              return 'neutral';
            }
            return '';
          }, function(response) {
            // Error
            console.log(response.data)
          });


        return sentiment;

      }
    }

  }
</script>

<style>
  #app {
    font-family: 'Avenir', Helvetica, Arial, sans-serif;
    -webkit-font-smoothing: antialiased;
    -moz-osx-font-smoothing: grayscale;

    color: #2c3e50;
    margin-top: 60px;
  }

  body {
    margin: 150px;
  }

  h1, h2 {
    font-weight: normal;
  }

  p {
    display: inline-block;
    margin: 5px 10px;
    text-align: justify;
  }

  a {
    color: #42b983;
  }

  textarea {
    width: 80%;
    height: 70px;
  }

  blockquote {
    background: #f9f9f9;
    border-left: 10px solid #ccc;
    margin: 1.5em 10px;
    padding: 0.5em 10px;
    quotes: "\201C" "\201D" "\2018" "\2019";
    font-size: 1.8em;
  }

  blockquote:before {
    color: #ccc;
    content: open-quote;
    font-size: 4em;
    line-height: 0.1em;
    margin-right: 0.25em;
    vertical-align: -0.4em;
  }

  blockquote p {
    display: inline;
  }

  .negative {
    color:red
  }

  .neutral {
    color: gray;
  }

  .positive {
    color: green;
  }
</style>
