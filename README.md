# llamada
A simplified, terminal-based interface to OpenAI-compatible LLM API endpoints.  This software provides a command shell of sorts against an OpenAI-compatible API endpoint.  

## What?

Yeah, it talks to your LLM endpoint.  You can run it against something like *LiteLLM*, directly against *Gemini*, or *Groq* (The one with a **q**, not the one with extra racism, though I suppose it might work with that one too), or *OpenRouter*.  Probably most any of the large services will work, as will most anything you might be running on your own.  It gives you a command-prompt that manages multiple sessions, each with their own history, potentially with different API credentials and different endpoints and different sets of loaded tools.  It supports local tool-calling, for *WASI* tools, in particular, by way of WASMEdge.  I also has a couple built-in tools for managing tool discovery.  This seems to work and not confuse the LLMs too much.

### Prerequisites

   * *Python* 3 and standard modules
   * The *OpenAI* module for *Python*
   * *wasmedge* for tool calling.  You can probably use the basic functionality without it, or you can modify the code and use another *WASM* runtime.
   * [*llamada_tools*](https://github.com/kemenril/llamada_tools), optionally.  This is a related project which implements a simple set of tools in *Rust*, intended to be built into *WASM* files. and used to exchange data with the LLMs, or perhaps eventually help with other tasks.

You also need API access.  If you don't have your own LLM, *Gemini*, *Groq*, and *OpenRouter* all provide decent enough free options which you could use.  You'll need to know the base URL and have an API key handy.  You could have more than one, and set up one or more separate sessions for each.

### Installation

There really isn't a procedure for this at the moment.  Put the script in a directory that is in your path.  When you first run it, if the directories don't exist, it will create _~/llamada_, and _~/llamada/sessions_, with a session directory in it for the default session.  You'll also want to add a _~/llamada/config_, which is the main configuration file.  

You'll be able to override everything in it by adding a configuration for each session, if you like, but this can contain default endpoint information and so on.  Here's an example, which will be included in the repo separately:

      [DEFAULT]
          URL                 = https://llmendpoint.local.net/
          KEY                 = sk-ThisIsntARealLLMAccessToken
          Streaming           = Yes
          Model               = gpt-4o-mini
          # If you want, model parameters can also be defined
          # Model.temperature = 1.1
          # Model.top_p       = 0.3
          # Model.max_tokens  = 800
          ValidateSSL         = Yes
          Verbose             = No

There are some other options you can include, for loading tools automatically, for example, but this is a good start.

You may also want to create _~/llamada/tools_, and install some *WASI* binaries there, along with their respective *JSON* schema files.  See the [*llamada_tools*](https://github.com/kemenril/llamada_tools) project.

### Usage

There are some command-line options:

      $ llamada --help
      usage: llamada [-h] [-P | --progress | --no-progress] [-U URL] [-K KEY]
               [-M MODEL] [-S SESSION] [-v] [-V] [-i] [--lsmodels]
               [--lssessions] [-w]
               [question ...]
      
      A simple command-line interface for the OpenAI API.

      positional arguments:
       question

      optional arguments:
        -h, --help            show this help message and exit
        -P, --progress, --no-progress
        -U URL, --url URL     The BASE API URL.
        -K KEY, --key KEY     The API key.
        -M MODEL, --model MODEL
                              Select the model
        -S SESSION, --session SESSION
                              Use named session, instead of DEFAULT
        -v, --version         show program's version number and exit
        -V, --verbose         Print the reasoning ahead of the actual response.
        -i, --interactive     Leave the session open and allow interaction.
        --lsmodels            List the available models in the remote endpoint and
                              exit.
        --lssessions          List the current sessions and exit.
        -w, --wrconfig        Write a configuration file matching the given command-
                              line arguments into the session directory.

There is also an online help system which you can access by just typing **help**; the command interpreter will intercept commands defined in the system and will not send them to the remote LLM.  LLMs are smart enough to handle phrasing, so if you want to ask the LLM to do something, but would normally start the sentence with a command-word, just rephrase it.

A simple interactive session might look like this:

      $ llamada -S test3
      Activating internal tool list_tools
      Activating internal tool get_tool_schema
      Connecting to https://api.groq.com/openai/v1
      No question given, forcing interactive mode.
      gpt-4o-mini> Hello, if you had to frob a glumbrap, what would be your preferred
      means of emblasticization?
      ↙
      If I were to frob a glumbrap, my preferred means of emblasticization would
      likely involve a careful consideration of the glumbrap's unique characteristics 
      and the desired outcomes of the frobbing process. I'd ensure to use 
      comprehensive methodologies that align with current best practices in glumbrap 
      handling. 

      Given that this is a whimsical and hypothetical scenario, I'd also be open to 
      innovative techniques that might emerge during the process. Ultimately, 
      collaboration with fellow frobbists could yield the most effective and creative 
      results in achieving optimal emblasticization.

      Would you like to explore anything specific about this process or related 
      topics?
      gpt-4o-mini>
