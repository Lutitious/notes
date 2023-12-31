- Two regex are equivalent provided:
	- $${r}_{1}\equiv{r}_{2}\stackrel{def}{=}L({r}_{1}) = L({r}_{2})$$
- All of the following are equivalent:
	- $$(a + b)+ c \equiv a + ( b + c)$$
	- $$a + a \equiv a$$
	- $$a + b \equiv b + a$$
	- $$(a *  b) * c \equiv a * ( b * c)$$
	- $$c * (a + b) \equiv (c * a) + (c * b)$$
	- $$1 \equiv 0^{*}$$
	- $$1^{*} \equiv 1$$
- The following are not equivalent
	- $$a * a \not\equiv a$$
	- $$a + (b *c) \not\equiv (a+b) * (a+c)$$
	- $$a * 0 \not\equiv a$$
	- $$a +1 \not\equiv a$$
	- $$ 0^{*} \not\equiv 0$$
-
- Important equivalences for regex matchers:
	- $$r + 0 \equiv r$$
	- $$0 +r \equiv r$$
	- $$r * 1 \equiv r$$
	- $$1 *r \equiv r$$
	- $$r * 0 \equiv 0$$
	- $$0 * r \equiv 0$$
	- $$r + r \equiv r$$
- Example simplification 1:
	- $$((1*b)+0)*r ⇒ ((\underline{1*b})+0)*r$$
	- $$=(\underline{b+0})*r$$
	- $$= b * r$$
- Example simplification 2:
	- $$((0*b)+0)*r ⇒ ((\underline{0*b})+0)*r$$
	- $$=(\underline{0+0})*r$$
	- $$= 0 * r$$
	- $$= 0$$
-
- The semantic derivative of a language:
	- Basically take the words that begin with the letter c in the set A then remove the first c and the remainders form a new set which is our semantic derivative
	- $$Der\ c\ A \stackrel{def}{=}\{s|c::s \in A\}$$
	- For $$ A = \set{foo,bar,frak}$$
		- $$Der\ f\ A= \set{oo,rak}$$
		- $$Der\ b\ A= \set{ar}$$
		- $$Der\ a\ A= \set{}$$
	- We can extend that definition to strings:
		- Look for all the strings that start with s then remove the first instance of s
		- $$Ders\ s\ A \stackrel{def}{=}\{s' | s@s' \in A\}$$
-
- Brzozowski's Algorithm(whether regex can match the empty string):
	- $$nullable(0) \stackrel{def}{=} false$$
	- $$nullable(1) \stackrel{def}{=} true$$
	- $$nullable(c) \stackrel{def}{=} false$$
	- id:: 654e6f2b-72d2-45d1-b554-87ece40fd965
	  $$nullable(r_{1}+r_{2}) \stackrel{def}{=} nullable(r_{1}) \vee nullable(r_{2})$$
	- $$nullable(r_{1}*r_{2}) \stackrel{def}{=} nullable(r_{1}) \wedge nullable(r_{2})$$
	- $$nullable(r_{*}) \stackrel{def}{=} true$$
- If r matches the string c::s then the alogrithm to match just s is der c r
- $$der\ c(0)\stackrel{def}{=}0$$
- $$der\ c(1)\stackrel{def}{=}0$$
- $$der\ c(d)\stackrel{def}{=}$$
	- if $$c=d$$ then $$1$$
	- else $$0$$
- $$der\ c(r_1 + r_2)\stackrel{def}{=}der\ c(r_!)+der\ c(r_2)$$
- $$der\ c(r_1 * r_2)\stackrel{def}{=}$$
	- if $$nullable(r_!)$$ then $$der \ c(r_1) *r +der\ c(r_2)$$
	- else $$der\ c(r_1)*r_2$$
-
- $$der\ c(r^*) \stackrel{def}{=}(der\ c(r)) * r^*$$
- $$ders\ []\ r \stackrel{def}{=} r$$
- $$ders\ (c::s)\ r \stackrel{def}{=} ders\ s(der\ c\ (r))$$
- Derivative Example:
	- $$der\ a\ {((a * b)+b)}^{*}⇒ der\ a\ \underline{{((a * b)+b)}^{*}}$$
	- $$=(der\ a\ \underline{((a * b)+b)}*r)$$
	- $$=(der\ a\ (\underline{a+b}) + (der\ a\ b)*r)$$
	- $$=(((der\ a\ (\underline{a}))*b) + (der\ a\ b)*r)$$
	- $$= ((1*b) + (der\ a\ (\underline{b})))*r$$
	- $$= ((1*b) + 0)* r$$
- The complete algorithm:
	- $$matcher\ r\ s \stackrel{def}{=} nullable(ders\ s\ r)$$
- Does $r_1$ match $abc$
	- Step 1 build derivative of $$a$$ and $$r_1$$: $$r_2 = der\ a\ r_1$$
	- Step 2 build derivative of $$b$$ and $$r_2$$: $$r_3 = der\ b\ r_2$$
	- Step 3 build derivative of $$c$$ and $$r_3$$: $$r_4 = der\ c\ r_3$$
	- Step 4 the string is exhausted. Test if r_4 can match the empty string: $$nullable(r_4)$$
	- Output: true or false
-
- For $$r^{n}$$ if $$n = 0$$ then $$nullable = true$$
- else $$nullable(r^{n}) = nullable(r)$$
-
- der for n times:
- Case n=0: $$der\ c\ 1 = 0$$
- Case n=1: $$der\ c\ r$$
- Case n=2:$$(der\ c\ r)*r$$
- Case n (where $$n \geq 1$$): $$(der\ c\ r)*r\{n-1\}$$