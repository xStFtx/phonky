<script>
  let messages = [
    { id: 1, sender: 'John', content: 'Hey, how are you?', time: '2:30 PM', isMe: false },
    { id: 2, sender: 'Me', content: 'I\'m good! Working on some code.', time: '2:31 PM', isMe: true },
    { id: 3, sender: 'John', content: 'That sounds fun! What are you building?', time: '2:32 PM', isMe: false }
  ];

  let newMessage = '';

  function sendMessage() {
    if (newMessage.trim()) {
      messages = [...messages, {
        id: messages.length + 1,
        sender: 'Me',
        content: newMessage,
        time: new Date().toLocaleTimeString('en-US', { hour: 'numeric', minute: '2-digit' }),
        isMe: true
      }];
      newMessage = '';
    }
  }
</script>

<div class="messages-app">
  <div class="chat-container">
    {#each messages as message}
      <div class="message {message.isMe ? 'sent' : 'received'}">
        <div class="message-content">
          <span class="sender">{message.sender}</span>
          <p>{message.content}</p>
          <span class="time">{message.time}</span>
        </div>
      </div>
    {/each}
  </div>
  
  <div class="message-input">
    <input 
      type="text" 
      bind:value={newMessage} 
      placeholder="Type a message..."
      on:keydown={(e) => e.key === 'Enter' && sendMessage()}
    />
    <button on:click={sendMessage}>Send</button>
  </div>
</div>

<style>
  .messages-app {
    height: 100%;
    display: flex;
    flex-direction: column;
  }

  .chat-container {
    flex: 1;
    padding: 15px;
    overflow-y: auto;
    display: flex;
    flex-direction: column;
    gap: 10px;
  }

  .message {
    display: flex;
    margin: 5px 0;
  }

  .message.sent {
    justify-content: flex-end;
  }

  .message-content {
    max-width: 70%;
    padding: 10px;
    border-radius: 15px;
    position: relative;
  }

  .sent .message-content {
    background: #007AFF;
    color: white;
    border-bottom-right-radius: 5px;
  }

  .received .message-content {
    background: #E9E9EB;
    color: black;
    border-bottom-left-radius: 5px;
  }

  .sender {
    font-size: 12px;
    opacity: 0.7;
    margin-bottom: 4px;
    display: block;
  }

  .time {
    font-size: 10px;
    opacity: 0.7;
    position: absolute;
    bottom: -15px;
    right: 5px;
  }

  .message-input {
    padding: 15px;
    display: flex;
    gap: 10px;
    background: #1a1a1a;
  }

  input {
    flex: 1;
    padding: 10px;
    border-radius: 20px;
    border: none;
    background: #2a2a2a;
    color: white;
  }

  button {
    padding: 10px 20px;
    border-radius: 20px;
    border: none;
    background: #007AFF;
    color: white;
    cursor: pointer;
    transition: opacity 0.2s;
  }

  button:hover {
    opacity: 0.8;
  }
</style>
