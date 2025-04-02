# Prompt Engineering Techniques

## License and Citation
This project is released under the **Creative Commons Attribution 4.0 International (CC BY 4.0)** license.

Please cite if you find this useful. Thanks

```
@article{patil2025advancing,
  title={Advancing Reasoning in Large Language Models: Promising Methods and Approaches},
  author={Patil, Avinash},
  journal={arXiv preprint arXiv:2502.03671},
  year={2025}
}
```
For more details on the license, visit [CC BY 4.0](https://creativecommons.org/licenses/by/4.0/).

## Zero-Shot Prompting
Zero-shot prompting is the simplest approach: the prompt directly instructs the model to perform a task without providing any examples or demonstrations. In this setting, the model relies entirely on its pre-trained knowledge and instruction-following ability to respond​. For example, you might just ask, “Translate the following sentence into French:” followed by the sentence, and the model must do so based on learned knowledge. This method works well for general tasks but may struggle on highly specific or complex tasks since the model isn’t guided by any contextual examples. The primary benefit is its simplicity and broad applicability – you can prompt the model for anything “out of the box” and leverage its training data, without needing to hand-craft examples.


## Few-Shot Prompting (In-Context Learning)
Few-shot prompting involves giving the model a few examples or demonstrations of the task in the prompt before asking it to perform the task on a new input​. By showing input-output pairs or question-answer examples, we “prime” the model to understand the format and relevant features of the task. For instance, to teach an LLM a new word usage, you might provide one or two example sentences using that word, then ask it to produce another​. These demonstrations serve as context that the model uses to infer how to respond for the new query. Few-shot prompting significantly improves performance on complex or niche tasks compared to zero-shot, since the model can learn the pattern from examples (a phenomenon known as in-context learning). In essence, it’s a way to “condition” the model on the fly, often yielding more accurate or formatted outputs.


## Chain-of-Thought (CoT) Prompting
Chain-of-Thought prompting is a technique that has the model generate intermediate reasoning steps before giving a final answer​. Instead of answering directly, the model is prompted (usually with examples) to “think out loud,” producing a step-by-step explanation or calculation. This was introduced by Wei et al. (2022) and unlocks complex reasoning capabilities in large models​. For example, rather than directly responding to a math word problem, the model will articulate the solution process (e.g., computing subtotals, considering each condition) and then arrive at the answer. CoT prompting can be done in a few-shot manner by providing Q&A pairs with rationale, or even zero-shot (see below). The primary benefit is improved accuracy on tasks like math, logic, and commonsense reasoning, since the model can break the problem down and avoid leaps of logic​. CoT effectively makes the model’s latent reasoning explicit, leading to more interpretable and often more correct answers.


## Zero-Shot Chain-of-Thought
Zero-Shot CoT is a variant where we trigger step-by-step reasoning in a zero-shot setting by appending a simple phrase to the prompt. Kojima et al. (2022) discovered that adding “Let’s think step by step.” to a question prompts models to produce a chain of thought without any examples​. In this approach, the model is instructed to articulate its reasoning process even though no worked-out examples are given. Surprisingly, this often leads to much better results on reasoning tasks than a direct zero-shot answer​. For instance, a math problem that the model initially gets wrong might be answered correctly if the “Let’s think step by step” cue is added, because the model then carefully works through each step​. This technique leverages the model’s learned reasoning abilities and is useful when no example demonstrations are available. The benefit is that it elicits CoT-style reasoning (and thus better accuracy) with minimal prompt engineering – essentially a one-line instruction acts as an automatic prompt for reasoning.


## Self-Consistency (CoT Self-Consistency)
Self-Consistency is a decoding strategy that builds on Chain-of-Thought prompting by having the model generate multiple independent reasoning chains for the same question and then aggregate their answers​. Instead of trusting a single step-by-step solution (which might go astray), the model samples several possible solution paths (via randomness in its generation) and arrives at potentially different answers. The final answer is chosen by majority vote or by selecting the most consistent outcome across these multiple chains​. This approach rests on the idea that while each individual chain-of-thought might have errors, the correct reasoning will appear frequently among many attempts, so the consensus answer is likely correct​. Empirically, self-consistency greatly improves accuracy on reasoning benchmarks by reducing the chance of following a lone, flawed chain of thought​. In short, it encourages the model to “think twice (or ten times) and answer once,” leading to more reliable results than naive single-pass CoT reasoning.


## Least-to-Most Prompting
Least-to-Most prompting (LtM) is a problem-solving strategy that explicitly decomposes a complex problem into a sequence of smaller sub-problems, solving them one by one from easiest (“least”) to hardest (“most”)​. The idea is inspired by how teachers might progressively lead students to a solution. In practice, the prompt first asks the model to generate or consider simpler preliminary questions or steps, then uses the answers to those as input for tackling the next, more advanced sub-problem​. This is somewhat like Chain-of-Thought, but with an important twist: each sub-step’s result is fed into the next prompt, rather than just being an internal reasoning trace​. By building the solution incrementally, the model can handle aspects of the problem in stages, which helps maintain focus and accuracy on each part. Least-to-Most prompting has been shown to achieve higher accuracy than standard one-shot reasoning on various tasks, as the model incrementally works up to the full solution. The benefit is particularly seen in very challenging tasks – breaking them down this way prevents the model from being overwhelmed or making a big leap that might go wrong.


## Self-Ask Prompting (Question Decomposition)
Self-Ask prompting is a technique where the model is prompted to ask itself intermediate questions as a means of breaking down a complex query​. Essentially, the model performs a Socratic dialogue internally: it poses a sub-question, answers it, possibly poses another, and so on, before finally answering the original question. For example, if asked, “Who was president when the Berlin Wall fell and what was the outcome?”, a self-ask approach might have the model first ask itself, “When did the Berlin Wall fall, and who was president at that time?”, answer that (1989 and President George H.W. Bush), then proceed to ask, “What happened as a result of the Berlin Wall falling?”, answer that, and finally synthesize the answers. This method improves multi-hop reasoning by explicitly prompting the model to decompose the problem into smaller parts​. It can be done with a single prompt that demonstrates the Q&A decomposition format​. The primary benefit is making the reasoning process more structured and interpretable, leading to better performance on complex questions that require combining multiple pieces of information or steps of reasoning.


## Generated Knowledge Prompting
Generated Knowledge Prompting involves asking the model to generate relevant background information or facts before attempting to solve a task or answer a question. In this approach, the prompt might be structured in two phases: first, “brainstorm” or produce some general knowledge about the topic, and second, use that knowledge to produce the final answer​. For instance, given a tricky commonsense question, the model would first be prompted to state some known facts or context (even if not provided explicitly in the input) that could be useful, and then, with those facts in hand (as part of the prompt now), answer the question. The idea, explored by Liu et al. (2022), is to see if an LLM can utilize its vast stored information by explicitly writing it out as a form of scratch work​. This often helps with tasks like commonsense reasoning or specialized Q&A where the model might otherwise forget or not realize which piece of its knowledge is needed. By generating its own supporting knowledge first, the model’s final responses become more accurate and grounded, as it has effectively reminded itself of relevant information. The benefit is similar to providing the model context, except here the model generates the context itself – leveraging its training data in a focused way.


## Tree-of-Thought (ToT) Prompting
Tree-of-Thought prompting is an advanced strategy that lets an LLM explore multiple reasoning paths in a branching tree structure rather than a single linear chain. This framework is analogous to a search tree: the model can branch out with different possible steps or ideas, evaluate intermediate outcomes, and even backtrack or try alternatives, much like planning moves in a game​. In practice, implementing ToT might involve iterative prompting where at each step the model proposes possible next moves or partial solutions, and a separate process (or the model itself) evaluates which branch to follow. The key distinction from standard CoT is that CoT is a linear step-by-step thought process, whereas ToT allows hierarchical, non-linear exploration of ideas (multiple “thoughts” can be pursued in parallel)​. This approach enables lookahead – the model can simulate several steps into different solution paths – and choose the most promising route. Tree-of-Thought prompting has shown the ability to dramatically improve performance on tasks that benefit from planning, search, or extensive exploration (e.g. puzzle solving, games)​. Its primary benefit is that it combines the coherence of chain-of-thought reasoning with the power of branching search, leading to more robust problem-solving by considering many possibilities before finalizing an answer.


## ReAct Prompting (Reason + Act)
ReAct is a prompting technique that interleaves reasoning steps with action commands, enabling an LLM to not only think through a problem but also to interact with external tools or environments as part of its reasoning process​. A ReAct prompt typically provides a few-shot demonstration of an agent solving tasks by alternating between thoughts (natural language reasoning) and acts (commands like API calls, tool usage, or queries), along with the environment’s responses​. For example, a ReAct demonstration for a QA task might show a model first pondering (“Thought: I should search the wiki for X”), then an action (“Action: search(X)”), then receiving a result, then another thought using that information, and so on, until it gives an answer. By combining these, the model learns to perform reasoning with the ability to gather information or make decisions during the process. The primary purpose is to reduce hallucination and improve task performance when external knowledge or stepwise decision-making is needed​. ReAct has been effective in tasks like factual question answering (where the model can call a search API to fact-check) and interactive decision-making, leading to more accurate and interpretable solutions than reasoning alone​. In summary, ReAct prompting turns an LLM into an agent that can think and act, leveraging tools while maintaining a chain-of-thought.


## Prompt Chaining (Multi-step Prompting)
Prompt chaining is a pipeline approach where a complex task is broken into a series of smaller prompts, arranged such that the output of one prompt becomes the input to the next​. Instead of trying to get the model to do everything in one giant prompt, you orchestrate a chain of prompts, each handling a subtask or transforming the data step by step. For instance, in a document question-answering scenario, you might first prompt the model to extract relevant facts or quotes from the document, and then in a second prompt feed those extracted pieces to the model to formulate the final answer​. By doing this, each prompt in the chain has a focused job, and the model’s outputs can be checked or modified at each stage. This technique improves reliability and transparency: you can see exactly what the model produced at each intermediate step and debug or adjust if necessary​. It also helps with controllability, since each stage can apply formatting or constraints (e.g., the first prompt always outputs a list of bullet points which the second prompt then uses). Prompt chaining is widely used in building complex LLM applications (often via frameworks like LangChain) because it allows the developer to guide the model through a multi-step workflow. The benefit is achieving sophisticated results that would be hard to get via a single-shot prompt, all while mitigating errors by tackling one subtask at a time.


## Automatic Prompt Engineer (APE)
Automatic Prompt Engineer (APE) is a method for automating the creation and selection of effective prompts using the help of language models themselves. Proposed by Zhou et al. (2022), APE treats prompt generation as an optimization problem: an LLM is used to generate many candidate instructions/prompts for a task, and those candidates are then tested to see which works best​. The typical process is: given a task description or a few input-output examples (for context), an LLM “brainstorms” possible prompts that might solve the task​. These candidate prompts (often in the form of instructions) are then fed to a target model (which could be the same or a smaller model) along with some test input; the outputs are evaluated against desired outcomes, and the prompt with the highest score is selected​. In essence, the model is acting as a prompt engineer – hence the name – trying out different ways to ask itself to get the best result. APE can discover prompts (including ingenious instructions) that humans might not think of; for example, it found an improved variation of the “let’s think step by step” prompt that yielded better reasoning performance​. The primary benefit of APE is removing the guesswork for users: it leverages the model’s own linguistic creativity to find an optimal prompt automatically, which can dramatically improve zero-shot performance on a given task without manual tuning.


## Automatic Chain-of-Thought (Auto-CoT)
Automatic Chain-of-Thought prompting (Auto-CoT) is a technique to automatically generate chain-of-thought examples for few-shot prompts, rather than relying on hand-crafted solutions. Manually writing CoT exemplars for a new task can be labor-intensive; Auto-CoT proposes to have the model do it on its own​. The approach works by first sampling a set of questions (related to the task or even generic ones) and then using the model with a zero-shot CoT cue (like “Let’s think step by step”) to produce reasoning chains for those questions​. These generated question-answer with reasoning pairs are then filtered or selected (with an emphasis on diversity to cover different reasoning patterns​) to serve as the demonstrations for the final prompt. In other words, the model “teaches itself” by creating a few examples of how to think through problems one-by-one​. Even if some of these auto-generated reasoning chains have mistakes, using several diverse examples dilutes the effect of any single error. Empirical results have shown that Auto-CoT can match or even exceed the performance of human-designed CoT examples on many tasks​, significantly reducing the human effort needed in prompt engineering. The benefit of Auto-CoT is that it scales prompt engineering to many tasks quickly – you don’t need an expert to carefully craft reasoning examples for each new scenario, as the model can bootstrap them itself and still achieve strong reasoning performance.


## Chain-of-Code (CoC) Prompting
Chain-of-Code prompting is an extension of chain-of-thought ideas into the domain of programming: the model uses code generation as a means of reasoning and problem solving. In a Chain-of-Code approach, the LLM is prompted to produce code or pseudocode that would solve the problem at hand, and then that code is executed (or at least conceptually “run”) to obtain a result​. The process typically has two phases: (1) Generation: given a question, the model writes a snippet of code (e.g., a Python script or calculation) that outlines the steps to solve the problem; (2) Execution: the code is executed with an interpreter, or if actual execution isn’t possible, the model itself simulates the execution and computes the outcome​. For example, for a math word problem, the model might generate a short Python program to do the arithmetic and logic, then run it to get the numeric answer. This approach leverages the precision and reliability of code – if the code is correct, the answer will be correct – and it allows the model to use tools like a Python interpreter as part of its reasoning. Chain-of-Code has been shown to significantly enhance reasoning on tasks where calculation or algorithmic precision is needed​. A related idea is the Program-Aided Language model (PAL), which similarly uses generated code execution to solve problems. The main benefit of CoC prompting is improved accuracy and the ability to handle more complex multi-step computation, since the model can offload the heavy lifting to code execution rather than juggling it all in pure natural language.


## Structured Chain-of-Thought (SCoT)
Structured Chain-of-Thought prompting is a technique where the intermediate reasoning steps are given a predefined structure or format to better suit the task, rather than free-form natural language thoughts. In code generation, for instance, SCoT was proposed to guide models to think in terms of programming structures (like functions, loops, conditionals) as they reason about a coding problem​. Instead of a loose chain-of-thought, the model is asked to outline the solution in a structured way (perhaps listing steps tied to code components) before writing the final code. By imposing a structure on the reasoning, we constrain the model to consider specific angles – e.g., in coding, to consider how to break a problem into functions or handle different branches of logic​. This method has been shown to yield higher-quality outputs. In one study, SCoT prompting for code generation significantly improved accuracy (Pass@1) compared to standard CoT, because the model’s reasoning was aligned with the inherent structure of code​. Structured CoT can also be applied to other domains, like having a model follow a template for logical arguments or decision steps. The primary benefit is increased coherence and relevance of the reasoning process: by following a structured schema, the model is less likely to produce tangential or disorganized thoughts, and more likely to hit all the key parts of the solution.


## Modular-of-Thought (MoT) Prompting
Modular-of-Thought prompting is a strategy that encourages an LLM to divide a task into modular components or subroutines, solving each component separately and then integrating them. In the context of programming, MoT has been used to have a model plan out a program as a set of functions (modules) it needs, implement each function, and then compose them for the final answer​. Essentially, the model first generates a kind of outline: “Here are the helper functions or sub-tasks I should solve.” Then it tackles each of those in turn (often via separate prompts or a structured single prompt), and finally uses the solutions to produce the overall result​. This is analogous to how a software engineer breaks a big coding task into multiple functions or classes. By doing so, the model’s “thought” becomes inherently modular – each part addresses a specific aspect of the problem. The MoT approach has been shown to improve the modularity and correctness of solutions on complex coding challenges, as the model is less prone to mix up different parts of the logic and can focus on one piece at a time​. More generally, Modular-of-Thought is beneficial for any complex task where a monolithic solution is hard to get right; it forces decomposition into manageable chunks. The benefit is better organization in problem solving: the model’s output (and reasoning) is cleaner, easier to follow, and often more accurate because each module can be verified or corrected independently before being assembled into the final answer.


## Persona (Role) Prompting
The persona pattern is a prompting technique where you assign the model a specific role or identity to influence the style and content of its response​. For example, you might begin the prompt with, “You are a seasoned detective...,” or “Act as a professional translator,” etc. By doing so, the model’s subsequent output will try to remain consistent with that persona’s knowledge, tone, and viewpoint​. This can be used to make the model adopt an expert tone (e.g., a medical doctor explaining an illness), a creative angle (e.g., a poet narrating an event), or any number of styles and perspectives. How it works: the system or user prompt explicitly says “You are X” or “Pretend you are Y,” sometimes followed by guidelines or behaviors expected from that role. The LLM, having seen lots of text from various perspectives, will then emulate the requested persona. The primary purpose is to tailor the response to a desired style or context without having to engineer that style into every request. It can also improve relevance – an “Expert” persona might produce more factual and precise answers​. Community users often find this helpful to get more engaging or suitable outputs. In short, persona prompting guides the model’s voice and approach, making interactions more controlled or entertaining depending on the chosen character, which enhances the usefulness and user-friendliness of the LLM’s responses.
