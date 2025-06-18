---
title: "Project Two"
summary: "Moderátor Twitch chatu s použítím AI"
date: "Nov 17 2024"
draft: false
tags:
- Svelte
- Sveltekit
- Typescript
- Tailwind
---

**Moderování živého chatu** je na platformě **Twitch** zajišťováno Moderátory. 
Moderátor musí ručně odstraňovat nevhodné zprávy, banovat uživatele a celkově spravovat chat na streamu Majitele.  
Často ale není Moderátor aktivní a nevhodné zprávy nemá kdo smazat. 

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
    code = request.args.get('code')
    if not code:
        return jsonify({"error": "Authorization code is missing"}), 400

    # Define Twitch OAuth parameters
    client_id = os.getenv('TWITCH_CLIENT_ID') # Client ID
    client_secret = os.getenv('TWITCH_CLIENT_SECRET') # Client Secret
    redirect_url = 'https://<REDIRECT_URL>/auth/twitch/callback'  # REDIRECT URL

    if not client_id or not client_secret:
        logging.error("Twitch Client ID or Client Secret is missing in environment variables.")
        return jsonify({"error": "Server configuration error"}), 500

    # Use asyncio to run the asynchronous function
    data = asyncio.run(exchange_code_for_token(code, client_id, client_secret, redirect_url))
    
    if 'access_token' in data and 'refresh_token' in data:
        user_access_token = data['access_token']
        refresh_token = data['refresh_token']
        expires_in = data.get('expires_in', 3600)
        
        # Save tokens or perform necessary updates
        update_user_tokens(user_access_token, refresh_token, expires_in)
        logging.info(f"Successfully obtained and updated User Access Token: {user_access_token}") ## TODO: remove this 

        # Redirect the user back to the frontend after successful authorization
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

