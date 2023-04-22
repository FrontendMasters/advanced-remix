# 06. Imperative Mutations

## 📝 Notes

## 🤓 Background

So far the only way we've made mutations for our app is by using forms where the
user actually has to click something to submit the mutation. But sometimes we
want to let our backend know something without the user having to click
anything. For example, on [kentcdodds.com/blog](https://kentcdodds.com/blog), I
wanted to track how many times a blog post was read without having readers
self-report by clicking a "I read this" button. If you're on one of the blog
posts for long enough to have read it and scroll through the whole then that
should count as a "read." So I could use
[`useFetcher().submit`](https://remix.run/docs/en/1.15.0/hooks/use-fetcher#fetchersubmit) +
`useEffect` to make this work. It's something like this:

```tsx
const action = ({ request, params }) => {
  const userId = await getUserId(request);
  const formData = await request.formData();
  const intent = formData.get("intent");
  if (intent === "mark-as-read") {
    const slug = params.slug;
    await markAsRead({ userId, slug });
    return new Response("success");
  }
  throw new Error("Unknown intent");
};

function BlogPostRoute() {
  // stuff...

  const readFetcher = useFetcher();
  useEffect(() => {
    if (isRead) {
      readFetcher.submit({ intent: "mark-as-read" }, { method: "post" });
    }
  }, [readFetcher, isRead]);

  // more stuff...
}
```

You could also use the
[`useSubmit`](https://remix.run/docs/en/1.15.0/hooks/use-submit) hook to
accomplish this:

```ts
const submit = useSubmit();
useEffect(() => {
  if (isRead) {
    submit({ intent: "mark-as-read" }, { method: "post" });
  }
}, [submit, isRead]);
```

The difference is that `useFetcher().submit` is for making fetch requests
_without_ a navigation and `useSubmit()` is for making fetch requests _with_ a
navigation. So in my case, the `useFetcher` approach is more appropriate.

## 💪 Exercise

Because this app deals with financial and customer data, we don't want anyone
snooping if a user leaves their computer unattended. So we want to log the user
out after a time.

We've already got the modal and logic around the timeout implemented for you.
Your job is the Remix bit (of course). You need to post to the `/logout` route
to get the user logged out.

In this case, we're doing a navigation, so you should use the `useSubmit()` API.

## 🗃 Files

- `app/root.tsx`

## 🦉 Elaboration and Feedback

After the instruction, if you want to remember what you've just learned, then
fill out the elaboration and feedback form:

https://ws.kcd.im/?ws=Advanced%20Remix%20%F0%9F%A6%B8&e=06.%20Imperative%20Mutations&em=
