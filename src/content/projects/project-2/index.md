---
title: "Projekt AI Moderátor"
summary: "Moderátor Twitch chatu s použítím AI"
date: "Nov 17 2024"
draft: false
tags:
- Svelte
- Sveltekit
- Typescript
- Tailwind
---

## Motivace a cíl projektu
Moderování živého chatu na platformě Twitch je úkolem pro moderátory, kteří musí ručně odstraňovat nevhodné zprávy, blokovat uživatele a celkově spravovat konverzaci v chatu daného kanálu. Problém nastává ve chvíli, kdy moderátor není aktivní a v chatu se začne objevovat nežádoucí obsah, který nemá kdo odstranit.
 

Hlavním cílem tohoto projektu bylo navrhnout a vyvinout funkční full-stackovou aplikaci, která by tento problém řešila, a zároveň si v praxi vyzkoušet práci s moderními technologiemi.

---
## Použité technologie a zhodnocení

**Backend**: _Python_ & [Flask](https://flask.palletsprojects.com/en/stable/)  
Pro backend jsem zvolil Python s microframeworkem Flask. Tato volba se ukázala jako ideální – práce s Flask je velmi intuitivní, což mi umožnilo rychle postupovat a vyvíjet.

**Frontend**: [React](https://react.dev/)  
Vzhledem k předchozí zkušenosti s tvorbou mobilních aplikací jsem zvolil React. Sice React není kvůli své komplexnosti a velikosti nejlepší volbou ale v tomto případě jsem zvolil mně známou technologii.

**Databáze**: [PostgreSQL](https://www.postgresql.org/)  
Pro databázi jsem se rozhodl hostovat vlastní instanci PostgreSQL. Toto řešení zpětně hodnotím jako chybný krok. Využití platformy jako Supabase by celý proces výrazně zjednodušilo a ušetřilo čas.

**Ověřování a správa uživatelů**: [Clerk](https://clerk.com/)  
Správa uživatelů pro mě byla novou oblastí, proto jsem zvolil službu Clerk. S tímto řešením jsem byl spokojený – implementace je jednoduchá a vše fungovalo spolehlivě.  

**Platby**: [Stripe](https://stripe.com/en-cz)  
Pro zpracování plateb jsem implementoval bránu Stripe, která je v této oblasti zavedeným standardem.  


**Největší výzvy**  
Při zpětném pohledu bylo nejnáročnější pochopit a správně implementovat proces autentizace přes Twitch (OAuth) a napojení na platební systém Stripe, který jsem používal poprvé. Potom už byla tvorba frontendu jednodušší.

---

Looking back, the biggest struggles was to figuring out the **Twitch OAuth** process and implementing **Stripe** payments. After figuring out both I just needed to create the frontend.
I had a recent experience with making a mobile app using React so I used it for the frontend. This was probably not the perfect choice because 
of how bloated React is but I just wanted to use something I'm familiar with. No need to overcomplicate things.

For database I hosted my own **PostgreSQL** instance - this was definetly the wrong move as working with Supabase would be easier.  
User management was new to me so I used [Clerk.](https://clerk.com/) I was very happy with it, simple to implement and worked well for me.  
To collect payments I used [Stripe](https://stripe.com/en-cz) as this is the standard.


**Backend** = Python a [Flask.](https://flask.palletsprojects.com/en/stable/)  
**Frontend** = React


Při cestě vlakem mě napadlo moderování automatizovat. Ještě na cestě jsem udělal jednoduchý prototyp.  
Nejdříve bylo potřeba zaregistrovat aplikaci na https://dev.twitch.tv/ abych mohl použít [Twitch API.](https://dev.twitch.tv/docs/api/)  

#### Každá zaregistrovaná aplikace má 4 hlavní údaje:
1. Name - Jméno Aplikace
2. OAuth REDIRECT URLs - Na tuto adresu přesměruje Twitch uživatele po autorizování Aplikace. 
3. Client ID
4. Client Secret  

Po registrování aplikace jsem získal **Client ID** a **Client Secret** které se používají pro autorizaci [Twitch OAuth.](https://dev.twitch.tv/docs/authentication/getting-tokens-oauth/) 



```python
@app.route('/auth/twitch/callback', methods=['GET'])
def twitch_callback():
    # Get the authorization code and state from the query parameters
    auth_code = request.args.get('code')
    if not auth_code:
        return jsonify({"error": "Authorization code is missing"}), 400

    # Define Twitch OAuth parameters
    client_id = os.getenv('TWITCH_CLIENT_ID') # Client ID
    client_secret = os.getenv('TWITCH_CLIENT_SECRET') # Client Secret
    redirect_url = 'https://<REDIRECT_URL>/auth/twitch/callback'  # REDIRECT URL

    if not client_id or not client_secret:
        logging.error("Twitch Client ID or Client Secret is missing in environment variables.")
        return jsonify({"error": "Server configuration error"}), 500

    # Use asyncio to run the asynchronous function
    data = asyncio.run(exchange_code_for_token(auth_code, client_id, client_secret, redirect_url))
    
    if 'access_token' in data and 'refresh_token' in data:
        user_access_token = data['access_token']
        refresh_token = data['refresh_token']
        expires_in = data.get('expires_in', 3600)
        update_user_tokens(user_access_token, refresh_token, expires_in)

        return redirect(f"{FRONTEND_URL}?authorized=true")
    else:
        return jsonify({"error": "Failed to obtain access token"}), 500

```

Or for a generic code block without syntax highlighting:

```markdown
hello 
```
This is a generic code block.
You can put any text here.

