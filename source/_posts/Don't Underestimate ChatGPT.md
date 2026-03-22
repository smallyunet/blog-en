---
title: Don't Underestimate ChatGPT
tags: AI
date: 2023-03-30 17:09:36
draft_date: 2023-03-30 10:32:51
---

Previously, I didn't understand how Filecoin manages disk space. Since it uses Proof of Space, the Sector must occupy disk space when it's created. How does Filecoin release the occupied space promptly when a user's file is stored in the Sector? Releasing the space every time a file is written seems too rigid.

I checked Filecoin's [documentation](https://docs.filecoin.io/basics/what-is-filecoin/storage-model/) and [specifications](https://spec.filecoin.io/systems/filecoin_mining/sector/), but I couldn't find a direct answer. Running a Filecoin node is quite costly, requiring high-end hardware and close monitoring of disk capacity during operation.

I happened to ask ChatGPT about it, and to my surprise, it provided a detailed explanation, making me look at ChatGPT in a new light. Filecoin doesn't release disk space in real-time. Instead, after a Sector is filled with files, there's a Sealing process, which seals the Sector and replaces the previously generated random files with real data for mining and earning rewards.

I once heard in a video that Filecoin's disk utilization is only 50%, but I didn't understand why. Now it makes sense; before Sealing, the Sector occupies twice the disk space.

What's most surprising is ChatGPT's capability. In retrospect, if I had an experienced Filecoin researcher, they could easily answer my question. But the challenge is finding such an expert willing to answer my questions. Now I have found one.

On December 21, 2020, I wrote on [Weibo](/micro-blog/#2020-32):

> Simple memory has no meaning. Memorizing content from books and the internet only makes one "appear" knowledgeable, deceiving the ignorant. If we think of computers and the internet as another form of life, they would be more knowledgeable than any human.

Unexpectedly, such "life" has now appeared. ChatGPT integrates professional knowledge from various fields and can converse in human language. I always knew that merely increasing my "dead knowledge" reserve was useless because there will always be someone you can't surpass, even if that someone isn't human.

Recently, there was a case of ChatGPT surpassing human abilities: [How to view a man's pet dog being diagnosed by a veterinarian and later saved by asking GPT-4?](https://www.zhihu.com/question/592511373). The gist is, due to limited veterinary experience, the vet focused only on one possible cause of the dog's illness. Later, the owner let ChatGPT analyze the dog's health indicators, and ChatGPT provided two possibilities. At the hospital, it turned out to be the second one. Unlike vets, ChatGPT isn't limited by past experiences, making it appear more knowledgeable.

As we age, we must abandon extreme thinking. We cannot believe AI will replace humans because current AI lacks true intelligence. Nor can we deny that AI excels in certain areas and can be a great assistant to humans.

The most advanced large language model now is GPT-4, which requires a $20 per month subscription on the ChatGPT website. The free preview version is GPT-3.5, which is also impressive. When people thought AI chat was limited to Microsoft's Xiaoice and Apple's Siri, GPT-3.5 emerged, gaining widespread attention. I used GPT-3.5 for various tasks, such as generating positive podcast reviews for friends and completing programming assignments for vocational school students.

ChatGPT's translation ability is also strong, often surpassing Google Translate. For instance, Google once rendered a phrase meaning a fallback safeguard as "Pocket Mechanism," whereas ChatGPT translated it as "fallback mechanism," clearly showing stronger semantic understanding. Another example is the breed name "Ragdoll cat," which Google translated awkwardly while ChatGPT got it right, again showing a significant difference in accuracy.

However, GPT-3.5 has been criticized for "speaking nonsense seriously." As a language model, programmers can understand why this happens—it's like choosing from a set of templates, selecting the best-sounding answer from existing language templates. GPT-3.5 seems to work this way. Therefore, relying on GPT-3.5 for knowledge carries significant risk; it can provide correct information but also present fabricated content as knowledge.

Google's large language model [Bard](https://bard.google.com/) has comprehensive abilities inferior to ChatGPT, weak code handling capabilities, and also suffers from "speaking nonsense seriously" and often "answering off-topic." Chinese products are even less competitive.

Compared to GPT-3.5, GPT-4 should be improved in all aspects. While it's unclear how much the accuracy of information has improved, its language understanding is indeed superior to GPT-3.5, as discussed [here](https://v2ex.com/t/927744). GPT-4 uses a different training model architecture than GPT-3.5, and Microsoft has released a [technical paper](https://arxiv.org/abs/2303.12712) on GPT-4. The paper indicates that GPT-4 supports multi-modality, meaning it can generate statistical graphs based on input data, which may not yet be publicly available.

GPT-4's overwhelming capabilities have raised many concerns, even prompting Elon Musk to sign an [open letter to pause training AI more powerful than GPT-4](https://futureoflife.org/open-letter/pause-giant-ai-experiments/). No one knows whether GPT-4's emergence will benefit or harm human society.

Many products have already integrated GPT's capabilities. Microsoft's Office suite was the first to support it, being a self-developed product. It's uncertain how effective it will be in actual use, but it could revolutionize many repetitive tasks, with GPT capable of handling them more effectively. You don't need to know how to write a particular function in Excel; with GPT, you can describe it in natural language. Future computer education might include a new subject: how to communicate efficiently with ChatGPT.

Microsoft's Bing search engine has also incorporated GPT capabilities. Without GPT, Bing's search results cannot compete with Google. With GPT, Bing gains the ability to search using natural language. New Bing works by having GPT search the content, summarize it, and then describe it in natural language. The advantage is that GPT presents correct content, but the downside is slower processing. While it's worth trying, for programmers, Google remains the go-to for quick solutions.

More exciting is the prospect of GPT-4 plugins, integrating GPT into more scenarios, such as replacing GitHub Copilot for code generation. Once you use Copilot, you can't go back. It was considered advanced to have automatic code completion, but soon after Copilot's release, GPT emerged. Copilot faces a threat and has introduced the Copilot X solution, but I have more faith in GPT's future. I've canceled my $10 monthly Copilot subscription and subscribed to ChatGPT Plus for $20 a month, believing GPT will shine in the future.

Using ChatGPT outside the US still has some hurdles. You need to access the ChatGPT website normally, register with a US phone number, and subscribe to Plus with a US bank card. After several steps, buying a pre-configured account for 150 RMB is the most cost-effective option. Otherwise, even with high costs, success isn't guaranteed.

You can buy a US phone number for 300 RMB from Ultra with no KYC, starting with +1, with long-term retention and a $3 monthly fee. With Wi-Fi Calling enabled, you get 100 free SMS. A US phone number is quite useful for opening a US PayPal account and using a US Apple ID with US PayPal. US PayPal can be linked to a domestic Visa credit card. Many IM apps also require a foreign phone number for verification.

For foreign bank cards, try Depay virtual cards, growing rapidly due to ChatGPT membership needs. You can recharge with cryptocurrency and spend in USD. However, ChatGPT orders have various risk control rules, with IP and order address inconsistencies potentially triggering risk controls, causing subscription failures. For just opening ChatGPT Plus, Apple Pay is available on iPhones accessing ChatGPT, and US PayPal can be tried. Overall, buying an account is the fastest and easiest way.

Additionally, text-to-image generation in the AI field is quite popular. I've tried several hot products.

- [DALL-E](https://labs.openai.com/) from the same company as GPT, but often produces distorted characters
- [Replicate](https://replicate.com/explore) offers various model trials, such as [stable-diffusion](https://replicate.com/stability-ai/stable-diffusion), with the same issue of generating strange characters
- [Midjourney](https://www.midjourney.com/) operates on Discord, producing quality images but with small scenes

In my experience, current text-to-image generation isn't very effective, heavily depending on precise input descriptions matching the model's rules. Hopefully, future improvements with GPT's natural language capabilities will make text-to-image generation more user-friendly.

Having ChatGPT is like having an unprecedentedly powerful knowledge base with rich language capabilities, dedicated to serving you. Shouldn't you own one?
