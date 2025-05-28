## Mobile Compatibility

Unfortunately, the default user interface (UI) of RAGFlow is not optimized for mobile devices, making it challenging to use on smaller screens. 

A straightforward approach is to utilize the **embedding feature** provided by RAGFlow, which allows us to integrate the chat interface into a separate, mobile-optimized URL.
![image](https://github.com/user-attachments/assets/10d857aa-ccb5-455e-816e-880b1d4700b8)

### Step 1: Add Web Files

1. First, create a new directory to store the web files:

```bash
mkdir -p /Users/your_name/nginx-public/rag-mobile-ui
```

As a basic demonstration, create a html file within the directory and refer to `index.html`, which will house a very simple mobile-friendly chat interface. It utilizes the `iframe` code, which can be found in the RAGFlow chat section by hovering over and editing any chat assistant.

Grant read and execute permissions to the directory and its files:

```bash
chmod -R o+rX /Users/your_name/nginx-public
```



### Step 2: Reconfigure NGINX

Open the NGINX configuration file for editing:

```bash
sudo nano /opt/homebrew/etc/nginx/nginx.conf
```

Within the `server` block, add the following configuration to serve the mobile UI:

```nginx
location /mobile/ {
    alias  /Users/your_name/nginx-public/rag-mobile-ui/;
    index  index.html;
}
```

After making the necessary changes, restart NGINX to apply the configuration:

```bash
sudo nginx -s reload
```

You should now be able to access the mobile-optimized chat interface by visiting:
```https://your_domain.duckdns.org:5000/mobile/```. Start interacting with the RAGFlow system via the mobile-friendly chat interface.
