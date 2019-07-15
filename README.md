# Pay-Card
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <meta http-equiv="X-UA-Compatible" content="ie=edge" />
    <title>Mini App</title>
    <style>
      body {
        margin: 0;
        padding: 1em;
        background-color: #FFFFFF;
      }
      
      [data-cart-info],
      [data-credit-card] {
        transform: scale(0.78);
    	margin-left: -3.4em;
      }
      
      [data-cart-info] h2.mdc-typography--headline4 span {
        display: inline-block;
        vertical-align: middle;
      }
      
      .material-icons {
        font-size: 150px;
      }
      
      [data-credit-card] {
        width: 435px;
        min-height: 240px;
        border-radius: 10px;
        background-color: #5d6874;
      }

      [data-cc-info] input:focus,
      [data-cc-digits] input:focus {
        outline: none;
      }
      
      [data-cc-info] {
        margin-top: 1em;
      }
      
      [data-cc-digits] {
        margin-top: 2em; 
      }
      
      [data-cc-digits] input {
        color: white;
        font-size: 2em;
        line-height: 2em;
        border: none;
        background: none;
        margin-right: 0.5em;
      }
      
      [data-cc-info] input {
        color: white;
        font-size: 1.2em;
        border: none;
        background: none;
      }
      
      [data-cc-info] input:nth-child(2) {
        padding-right: 10px;
        float: right;
      }
      
      [data-pay-btn] {
        position: fixed;
        width: 90%;
        border: 1px solid;
        bottom: 20px;
      }

      .mdc-card__primary-action,
      .mdc-card__primary-action:hover {
        cursor: auto;
        padding: 20px;
        min-height: inherit;
      }
      
      [data-credit-card] [data-card-type] {
        transition: width 1.5s;
        margin-left: calc(100% - 130px);
        display: block;
        width: 120px;
        height: 60px;
      }

      [data-credit-card].is-visa {
        background: linear-gradient(135deg, #622774 0%, #c53364 100%);
      }

      [data-credit-card].is-mastercard {
        background: linear-gradient(135deg, #65799b 0%, #5e2563 100%);
      }

      [data-credit-card].is-visa [data-card-type],
      [data-credit-card].is-mastercard [data-card-type] {
        width: auto;
      }

      input.is-invalid,
      .is-invalid input {
        text-decoration: line-through;
      }

      ::placeholder {
        color: #fff;
      }
    </style>
  </head>
  <body>
    <div data-cart-info="">
      <h2 class="mdc-typography--headline4">
        <span class="material-icons">shopping_cart</span>
        <span data-bill>100</span>
      </h2>
    </div>
    <div data-credit-card="" class="mdc-card mdc-card--outlined">
      <div class="mdc-card__primary-action">
        <img src="http://placehold.it/120x60.png?text=Card" alt="Placeholder Image" data-card-type="">
        <div data-cc-digits="">
          <input type="text" size="4" placeholder="----">
          <input type="text" size="4" placeholder="----">
          <input type="text" size="4" placeholder="----">
          <input type="text" size="4" placeholder="----">
			</div>  
        <div data-cc-info="">
          <input type="text" size="20" placeholder="Name Surname">
          <input type="text" size="6" placeholder="MM/YY">
        </div>
      </div>
    </div>
    <button class="mdc-button" data-pay-btn>Pay & Checkout Now</button>
    
    <script>
      const supportedCards = {
        visa: 'visa', mastercard: 'mastercard'
      };
      
      const countries = [
        {
          code: "US",
          currency: "USD",
          country: 'United States'
        },
        {
          code: "NG",
          currency: "NGN",
          country: 'Nigeria'
        },
        {
          code: 'KE',
          currency: 'KES',
          country: 'Kenya'
        }, 
         {
          code: 'UG',
          currency: 'UGX',
          country: 'Uganda'
        },
        {
          code: 'RW',
          currency: 'RWF',
          country: 'Rwanda'
        },
        {
          code: 'TZ',
          currency: 'TZS',
          country: 'Tanzania'
        },
        {
          code: 'ZA',
          currency: 'ZAR',
          country: 'South Africa'
        },
        {
          code: 'CM',
          currency: 'XAF',
          country: 'Cameroon'
        },
        {
        code: 'GH',
          currency: 'GHS',
          country: 'Ghana'
        }
      ];
      
      const detectCardType = ({target}) => {
	    const imgUri = 'http://placehold.it/120x60.png?text=';
        const card = document.querySelector('div[data-credit-card]');
        const cardType = document.querySelector('img[data-card-type]');
        
        switch(target.value[0]){
          case '4':
            card.classList.remove('is-mastercard');
            card.classList.add('is-visa');
            cardType.src = imgUri + supportedCards.visa;
            return 'is-visa';
            break;
          case '5':
            card.classList.remove('is-visa');
            card.classList.add('is-mastercard');
            cardType.src = imgUri + supportedCards.mastercard;
            return 'is-mastercard';
            break;
          default:
            card.classList.remove('is-visa');
            card.classList.remove('is-mastercard');
            cardType.src = imgUri + 'Card';
            break;
        }
      }
      const validateCardExpiryDate = ({target}) => {
        let date = target.value;
        const isValid = expiryDateFormatIsValid(date);
        date = date.split("/");
        
        if(isValid){
          const now = new Date();
          if('20' + date[1] > now.getFullYear()){
            flagIfInvalid(target, true);
            return true;
          }else{
            if((parseInt(date[0]) + 1) >= now.getMonth()){
              flagIfInvalid(target, true);
              return true;
            }else{
              flagIfInvalid(target, false);
              return false;
            }
          }
        }
        
        flagIfInvalid(target, false);
        return false;
      }
      
      const validateCardHolderName = ({target}) => {
        const result = target.value.match(/\w{3,}\s\w{3,}$/g);
        if(result !== null){
          flagIfInvalid(target, true);
          return true;
        }
        flagIfInvalid(target, false);
        return false;
      }
      
      const validateWithLuhn = (digits) => {
        if(typeof digits == 'number') digits = digits.toString().split("");
        const len = digits.length;
        const parity = len % 2;
        let sum = 0;
        
        for(i = len - 1; i >= 0; i--){
          let d = parseInt(digits[i]);
          if(i % 2 == parity) d *= 2;
          if(d > 9) d -= 9;
          sum += d;
        }

        return (sum % 10 == 0) ? true : false;
      }
      
      const validateCardNumber = () => {
        const fields = [...document.querySelectorAll('div[data-cc-digits] input[type=text]')];
        const cardNumbers = fields.reduce((sum, item) => sum + item.value, '');
        const isValid = validateWithLuhn(cardNumbers.split(""));
        
        fields.forEach(item => flagIfInvalid(item, isValid));
      }
      
      const uiCanInteract = () => {
        const cardType = document.querySelector('[data-cc-digits] input:first-child');
        cardType.addEventListener('blur', detectCardType);
        
        const cardHolderName = document.querySelector('[data-cc-info] input:first-child');
        cardHolderName.addEventListener('blur', validateCardHolderName);
        
        const cardExpiryDate = document.querySelector('[data-cc-info] input:nth-child(2)');
        cardExpiryDate.addEventListener('blur', validateCardExpiryDate);

        const cardNumberBtn = document.querySelector('[data-pay-btn]');
        cardNumberBtn.addEventListener('click', validateCardNumber);
        
        const focusElement = document.querySelector('[data-cc-digits] input:nth-child(1)');
        focusElement.focus();
      }
      
      const displayCartTotal = ({results}) => {
        const [data] = results;
        const {itemsInCart, buyerCountry} = data;
        appState.items = itemsInCart;
        appState.country = buyerCountry;
        appState.bill = itemsInCart.reduce((sum, item) => {
          return sum + (item.price * item.qty);
        }, 0);
        appState.billFormated = formatAsMoney(appState.bill, appState.country);
        const databill = document.querySelector('span[data-bill]');
        databill.textContent = appState.billFormated;
        uiCanInteract();
      }
      
      const fetchBill = () => {
        const api = 'https://randomapi.com/api/006b08a801d82d0c9824dcfdfdfa3b3c';
        fetch(api).then((response) => {
          return response.json();
        }).then((data) => {
          displayCartTotal(data);
        }).catch((error) => {
          console.log(error);
        });
      }
      
      const formatAsMoney = (amount, buyerCountry) => {
        const country = countries.find(item => item.country == buyerCountry);
        
        if(country !== undefined) {
          return amount.toLocaleString('en-US', {
            style: 'currency',
            currency: country.currency
          });
        };
        return amount.toLocaleString('en-US', {
          style: 'currency',
          currency: countries[0].currency
        });
      }
      
      const flagIfInvalid = (field, isValid) => {
          return (isValid) ? field.classList.remove('is-invalid'): field.classList.add('is-invalid');
      }
      
      const expiryDateFormatIsValid = (target) => {
        const result = target.match(/(1[0-2]|0[1-9])\/([0-9]{2})$/);
        if(result !== null) return true;
        return false;
      }
      
      const startApp = () => {
        fetchBill();
      };
      
      const appState = {};
      
      startApp();
    </script>
  </body>
</html>



































































































































































































































































































































































































