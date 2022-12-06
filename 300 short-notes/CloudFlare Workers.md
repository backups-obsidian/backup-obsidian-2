---
created: 2022-11-28 22:20
updated: 2022-11-28 22:27
---
---
**Links**: [[300 home]]

---
## CloudFlare Workers
### How CloudFlare workers work?
- Instead of running your code in containers, it runs it in what they call *V8 isolates*.
- **Although its fast and lightweight its functionality is quite limited**.
- It’s *not the same as full-blown node.js* that can be run in containers, but rather a separated **V8 sandbox.**
	- This means that *you can only use built-in JS APIs, a subset of Web APIs* (those known from browser environments), and provided Cloudflare Workers APIs. 
	- So, e.g., you have to use Fetch API instead of `http` module, you can’t use native, CommonJS, and even ESM modules (even though it’s a browser-like V8 environment, ESM modules are disabled and can be used only through a bundler), *you can’t use most of NodeJS or browser globals (like `process`, `window`, `document`), and DOM-related APIs*.

### Workers KV
- KV provides really **fast read speeds**, thanks to running _“on the edge,”_ which simply means closest to the client. 
- This, on the other hand, means that (unfortunately) any **writes or deletes take longer** to propagate (i.e., achieve “eventual consistency”) across the whole network. 
- Workers KV are *great for any kind of intense-read scenarios* like static websites, configs, and user data, where changes don’t happen often

## References
- [Cloudflare Workers - the best serverless FaaS platform? (areknawo.com)](https://areknawo.com/cloudflare-workers-the-best-serverless-faas-platform/)
- [Cloudflare Workers - an introduction to Serverless - YouTube](https://www.youtube.com/watch?v=X-70tB9ONww) - Good basic tutorial.