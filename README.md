# Chatbot_Using_LangGraph_DeepSeek


1. Importing Required Libraries

from typing import List, Dict
from langgraph.graph import StateGraph, START, END
from langchain_ollama.llms import OllamaLLM
List and Dict from the typing module are used for type hinting in Python.

StateGraph, START, and END are imported from langgraph.graph.

OllamaLLM is imported from langchain_ollama.llms.

2. Defining State

class State(Dict):
    messages: List[Dict[str, str]]
A class State is defined, which inherits from Dict.

messages is a list of dictionaries, where each dictionary represents a message with a role (either 'user' or 'assistant') and content (the message text).

3. Initializing the State Graph

graph_builder = StateGraph(State)
llm = OllamaLLM(model='deepseek-r1:1.5b')
StateGraph is initialized with State.

OllamaLLM is initialized with a specific model ('deepseek-r1:1.5b').

4. Defining the Chatbot Function

def chatbot(state: State):
    response = llm.invoke(state["messages"])
    state["messages"].append({"role": "assistant", "content": response})
    return {"messages": state["messages"]}
The chatbot function takes a state as input.

The function uses llm.invoke() to get a response based on the current messages in the state.

The response is appended to the messages list with the role 'assistant'.

The updated state is returned.

5. Adding Nodes and Edges to the Graph

graph_builder.add_node("chatbot", chatbot)
graph_builder.add_edge(START, "chatbot")
graph_builder.add_edge("chatbot", END)
Adds the chatbot function as a node in the graph.

Adds edges to connect the start of the graph (START) to the chatbot node, and from the chatbot node to the end of the graph (END).

6. Compiling the Graph

graph = graph_builder.compile()
The graph is compiled and stored in the graph variable.

7. Streaming Graph Updates

def stream_graph_updates(user_input: str):
    state = {"messages": [{"role": "user", "content": user_input}]}
    for event in graph.stream(state):
        for value in event.values():
            print("Assistent: " + value["messages"][-1]["content"])
The stream_graph_updates function takes user_input as input.

Initializes the state with the user's input.

Streams updates from the graph and prints the assistant's response.

8. Running the Chatbot in a Loop
if __name__ == "__main__":
    while True:
        try:
            user_input = input("User: ")
            if user_input.lower() in ["exit", "quit", "q"]:
                print("Chatbot: Goodbye!")
                break
            stream_graph_updates(user_input)
        except Exception as e:
            print(f"An Error Occured.. {e}")
The main loop runs the chatbot, taking user input continuously.

If the user types 'exit', 'quit', or 'q', the loop breaks and prints "Goodbye!"

Any exceptions are caught and printed.

