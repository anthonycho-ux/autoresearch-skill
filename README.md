# Autoresearch

## Intention

Research that knows when to stop — and tells you why it didn't stop sooner.

---

The first time autoresearch ran too long, I didn't notice until the bill arrived. The loop had been chasing diminishing returns for hours, and nobody had told it to quit. It wasn't a failure of the tool — it was a failure of the design. The tool did exactly what I asked: keep researching until you have everything. But "everything" is a trap. You never have everything. You have to decide when the marginal gain isn't worth the marginal cost, and that decision requires judgment no search tool had ever asked for.

I built autoresearch because I wanted a research loop that would actually stop. Not because it ran out of queries, but because it measured something real: whether each iteration was still giving back meaningfully new information. When the speed of discovery plateaus and the entropy of new insights drops below a threshold, the loop should terminate — and it should say so clearly, with numbers, not just "I think I'm done."

The dual-metric termination was the key insight. Speed delta tells you whether the pipe is still flowing faster. Entropy tells you whether the water is still getting cleaner. Both have to fail together before the loop is allowed to stop. That was deliberately conservative — I wanted the loop to be aggressive about continuing, but not stupid about it.

The council layer (Karpathy, Klein, Gigerenzer) exists because I kept second-guessing the automatic termination. Would a real researcher quit here? Would they push one more query? The council is my answer to that anxiety — a panel of mental models that vote on whether the research has hit a reasonable stopping point. When they agree, I stop arguing with the tool.

What I didn't expect was how useful the logs are after the fact. The iteration-by-iteration record of entropy and speed tells a story about the domain itself — some topics plateau fast, others keep giving for twenty iterations. That historical record makes me smarter about estimating future research jobs.

The tool is called autoresearch because it runs itself. Set a seed query, walk away, come back to a synthesis. That's the point. Not "set a seed query, watch it anxiously, keep intervening." The loop handles the loop. You just read the results.
