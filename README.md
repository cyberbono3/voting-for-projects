# voting-for-projects

contract VoteForProject{
    address public owner;
    bytes32[] public projects;
    mapping(bytes32 => uint256) votes;

    struct voter {
        uint tokenBalance;
        
     }
    
   mapping (address => voter) public voterInfo;
    

  
  uint256 public totalTokens; // Total numberof tokens available for this election
  uint256 public balanceTokens; // Total number of tokens still available for purchase
  uint256 public tokenPrice; // Price per token
  
  uint256 public totalInvestedInWei;
  
 
 //tested in testrpc
 1000, 0.1, ["Paypal", "Ebay", "Amazon"]

 //require price per token >0
 //require pricePerToken > 0
function VoteForProject(uint tokens, uint pricePerToken, bytes32[] initialProjects) public  {
   
    require (tokens > 0);
    require (pricePerToken >0)
    totalTokens = tokens;
    balanceTokens = tokens;
    tokenPrice = pricePerToken;

    projects = initialProjects;
    owner = msg.sender;
    
}





// "Overstock"  tested in testrpc
//onlyOwner does not work


// new candidate can be created only by contract owner



function createProject(bytes32 projectName) private onlyOwner {
        require (projectExists(projectName) == false);
        projects.push(projectName);
}

//checks if project exists in ProjectList

//tested in testrpc
function projectExists(bytes32 projectName) constant public returns (bool) {
    for(uint i = 0; i < projects.length; i++) {
      if (projects[i] == projectName) {
        return true;
      }
    }
   return false;
}


//tested in testrpc
//how much votes particular candidate has
function getVotesCount(bytes32 projectName) constant public returns (uint256) {
    require (projectExists(projectName) == true);
    return votes[projectName];
}

  //test in testrpc 
function getAllprojects() constant public returns (bytes32[])  {
    return projects;

}

 
//?contribution notification
event Contribution(address indexed voterAddress, uint256 voterAmount, uint256 balance, uint256 investorTotal);   

//this fucntion is executed as soon as the contract accept ether payment "payable”
 function buy() payable  {
    uint tokensToBuy = msg.value / tokenPrice;
    require(tokensToBuy <= balanceTokens);// make sure that  token amount is avaiable for purchase
    voterInfo[msg.sender].tokenBalance += tokensToBuy;//add purchased tokens to voter.tokenBalance
    balanceTokens -= tokensToBuy;//dedudct purchased tokens from the avaiablem tokens to purchase
    address voterAddress = msg.sender;
    uint256 balance =  voterInfo[msg.sender].tokenBalance;
    totalInvestedInWei += msg.value;
   ? Contribution(msg.sender, msg.value, balance, totalInvestedInWei);      // contribution notification
    
}



 function voteForProject (bytes32 project, uint votesInTokens) public returns (uint) {
     //checks if candidate exists
     require(projectExists(project) == true);

     // Make sure this voter has enough tokens to vote
     require(voterInfo[msg.sender].tokenBalance >= votesInTokens);
   
   //append amount of tokens to voted project
    votes[project] += votesInTokens;
    
    //deduct voted tokens from voter's balance
    voterInfo[msg.sender].tokenBalance -= votesInTokens;

    return votesInTokens;
  }
  
