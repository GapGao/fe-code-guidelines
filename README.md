Front-end Code Guideline
========================

三只小猪的故事大家一定都听过，盖茅草房子或是木头房子虽然效率高，但因为不结实风一吹就倒，后期的维护成本会很高，盖水泥房子虽然工作量大前期效率低，但因为很坚固后期维护成本很低。

对于需要长时间持续迭代或者需要多人协作共同开发的大型项目，**维护**的成本比**开发**的成本更加重要。因此大型项目的代码规范应该是倾向于将**维护成本**置于**开发成本**之前。当维护成本和开发成本二者不可兼得时，尽量采取维护成本较低的方案。

其实不仅仅是写代码，很多地方都可以应用这个原则，比如对难以理解的逻辑添加注释，比如Merge Request写清楚本次改动的内容，比如编写单元测试等等。通常这些也是一个工程师的职业素养的体现，资深工程师的代码和应届毕业生的代码差距通常就在这里。

这里按照抽象基本从地道该分别列出了一些常见的前端代码的**Rule（规则）**、**Suggestion（建议）**和**Philosophy（哲学）**，范围包括了Javascript、CSS、React。

当然，凡原则必有例外，对于极端情况，也请不要拘泥，这个不是重点，这里就不过多解释了。
