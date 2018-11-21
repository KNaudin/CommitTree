# MerkleTree
This is an adaptation of Merkle Trees for a github commit-like implementation.
<img alt="not a merkle tree" src="https://github.com/KNaudin/MerkleTree/blob/master/images/merkle%20tree.png" width="500" height="400">
> Yes, I was bored

## What it does
Merkle trees are a type of binary tree that compute a hash to verify the integrity of a sequence.
In this project, each Tree contains branches made of nodes.
Branches are a HasmMap<String, Rc<RefCell<Node>>> (this is a smart pointer in Rust, don't be scared). The node linked
is always the head of the branch.
A node contains some data that can be a string, a float or an int. The node also knows its hash and its children if it has any.
If a node has any children, it will compute its hash according to their data. Else, it will compute from its own data.
<img alt="not a merkle tree" src="https://github.com/KNaudin/MerkleTree/blob/master/images/merkletreehash.png">

## Example
The following code
```
// Creating 4 nodes
let first_node = Node::new(Data::new(String::from("My age"), DataType::Integer(21)), String::from("Added my age."));
let second_node = Node::new(Data::new(String::from("A float"), DataType::Float(3.14)), String::from("Defined PI."));
let third_node = Node::new(Data::new(String::from("This is a"), DataType::String(String::from("Working tree."))), String::from("Commented line"));
let mut fourth_node = Node::new(Data::new(String::from("Random number"), DataType::Integer(21)), String::from("New branch!"));

// Create the tree with the first node as the 'master' head
let mut merkle_tree = Tree::new(first_node);
merkle_tree.push(second_node, String::from("master"));
merkle_tree.push(third_node, String::from("master")); // At this point, the third node is the 'master' head

// See [1]
merkle_tree.print_all_data(String::from("master"));

// Error because the dev branch doesn't exist [2]
let error = merkle_tree.push(fourth_node.clone(), String::from("dev"));
println!("{}", error);

// Last used branch was 'master' so the 'dev' branch will be set from the 'master' branch
merkle_tree.new_branch(fourth_node, String::from("dev"));

//See [3]
merkle_tree.print_all_data(String::from("dev"));
```
Will output:
```
[1]
Branch master
Commit 'Commented line'
Data name: 'This is a' Held information -> Working tree. hash: 7b74fc6204d5ef422b8b8ee10e5d64a8
Commit 'Defined PI.'
Data name: 'A float' Held information -> 3.14 hash: e39a411b54c3ce46fd382fef7f632157
Commit 'Added my age.'
Data name: 'My age' Held information -> 21 hash: 3c59dc048e8850243be8079a5c74d079
[2]
[error] branch does not exist
[3]
Branch dev
Commit 'New branch!'
Data name: 'Random number' Held information -> 21 hash: 1bd4a7d8d8f1686b3d1866376f9e8cd0
Commit 'Commented line'
Data name: 'This is a' Held information -> Working tree. hash: 7b74fc6204d5ef422b8b8ee10e5d64a8
Commit 'Defined PI.'
Data name: 'A float' Held information -> 3.14 hash: e39a411b54c3ce46fd382fef7f632157
Commit 'Added my age.'
Data name: 'My age' Held information -> 21 hash: 3c59dc048e8850243be8079a5c74d079
```
