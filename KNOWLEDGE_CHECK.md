# Lab 09: Knowledge check

**Answer each question below it, in your own words, in one to three sentences.** Replace the `...` below **Your answer:** with your text.

## 1. Credentials in the image?

Assume you want to share your image with others and make it publicly available on `ghcr.io`, so that anyone can pull it and run the analysis. A teammate suggests adding `COPY .aws /home/app/.aws` to the `Dockerfile`, so that nobody has to type `-v ~/.aws:/home/app/.aws:ro` any more. (This is a thought experiment: keep your own package private.)

**Your answer:** Give two reasons why this is a bad idea. Think of who could then read your credentials, and of what happens when your Learner Lab session ends.

Copying AWS credentials into the Docker image is a bad idea because anyone who downloads the public image could access the credentials, which creates a security risk. Also, the lab credentials expire when the session ends, so the image would contain outdated credentials and stop working.

## 2. Where did the Atlas password come from?

In Step 4, you ran the analysis once with `-v ~/.aws:/home/app/.aws:ro` only, without the three `-e` options. The container still wrote to your Atlas cluster.

*Hint:* Read `get_atlas_settings()` in `src/book_analysis/analyze.py`.

**Your answer:** Explain, step by step, where the container found the Atlas settings in that run.

The container accessed my AWS credentials through the read-only mounted .aws directory. It used those credentials to retrieve the mongodb_credentials parameter from AWS parameter store. The parameter contained my mongodb Atlas connection information, which allowed the container to connect to Atlas and save the analysis results without passing the environment variables directly.

## 3. Same tag, different image

Run `docker images book-analysis`, and note the image ID of `book-analysis:0.1.0`. Then change the `description` in the `LABEL` of your `Dockerfile`, build again with the same command, `docker build -t book-analysis:0.1.0 .`, and run `docker images book-analysis` again.

**Your answer:** Paste the image ID from before and after the rebuild. Why is it a problem for someone who uses your image that `0.1.0` now names a different image, and what should you have done instead?

**Original image ID:** `81b4c80b811a`

**New image ID:** `c14abc3dd0f4`

After changing the description label and rebuilding with `docker build -t book-analysis:0.1.0 .`, the image ID changed even though the tag stayed the same. This is a problem because someone using `0.1.0` might get a different image than they originally used, making it difficult to reproduce results. Instead, I should have given the updated image a new version tag, such as `0.1.1`, so each version can be identified the same way.
