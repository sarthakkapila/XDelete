Script to delete all X / Twitter tweets.

### How to run:
- Open Chrome and go to your X profile (https://x.com/USERNAME/with_replies)
- Open Chrome Developer Console. Copy/Paste the code:

```
class TwitterTweetDeleter {
  constructor() {
    this.processedButtons = new Set();
  }

  getDeleteButtons() {
    return Array.from(document.querySelectorAll('[data-testid="tweet"] [data-testid="caret"]'));
  }

  async delay(ms) {
    return new Promise(resolve => setTimeout(resolve, ms));
  }

  async deleteTweet(button) {
    button.click();
    await this.delay(250);

    const menuItems = Array.from(document.querySelectorAll('[role="menuitem"]'));
    const deleteOption = menuItems.find(item => item.textContent === 'Delete');

    if (deleteOption) {
      deleteOption.click();
      document.querySelector('[data-testid="confirmationSheetConfirm"]')?.click();
      await this.delay(3000);
    } else {
      const tweetContainer = button.closest('[data-testid="tweet"]');
      const unretweetButton = tweetContainer?.querySelector('[data-testid="unretweet"]');

      if (unretweetButton) {
        unretweetButton.click();
        await this.delay(250);
        document.querySelector('[data-testid="unretweetConfirm"]')?.click();
        await this.delay(3000);
      }
    }
  }

  async deleteAllTweets() {
    while (true) {
      const deleteButtons = this.getDeleteButtons().filter(button => !this.processedButtons.has(button));
      if (deleteButtons.length === 0) break;

      for (const button of deleteButtons) {
        this.processedButtons.add(button);
        await this.deleteTweet(button);
      }
    }

    console.log('All tweets deleted successfully!');
  }
}

const tweetDeleter = new TwitterTweetDeleter();
tweetDeleter.deleteAllTweets();

```
