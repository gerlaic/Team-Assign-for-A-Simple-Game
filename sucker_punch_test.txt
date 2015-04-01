import sys

#import data from keyboard (stdin)
team_count = 0 #init the team count
print("###Input the number of teams in the game###")
team_count = int(sys.stdin.readline())

list_units = [] #init the list

print("###Input each unit's attack power and defense power, seperated by a comment###")
print("###Input nothing to end###")
while True:
    line = sys.stdin.readline()

    if (line=='\n'):
        break
    
    attr = line.split(',')
    attack = int(attr[0])
    defense = int(attr[1][0:(len(attr[1])-1)])
    list_units.append([attack,defense])


#override parameters for easy testing
#list_units = [[5,1],[9,1],[7,4],[1,3],[1,3],[10,4],[4,5],[7,4]]
#team_count = 2

#list_units = [[6,10],[10,10],[4,8],[2,4],[8,4],[6,4],[8,2],[10,2],[8,8],[8,6],[2,10],[4,10],[10,6],[8,10],[4,6]]
#team_count = 3

#functions
#insert index into data
def insertIndex(data):
    
    for i in range(0,len(data)):
        data[i].append(i)
    return data

#get sum of a [[]] list in specific index    
def getSum(list,index):
    result = 0
    for element in list:
        result = result + element[index]
    return result

#fill up the units to make the function work for any amount of units
for i in range(0, len(list_units) - int(len(list_units)/team_count)*team_count):
    list_units.append([0,0,-1])

#insert index to the units
list_units_with_index = insertIndex(list_units)

#sort by attack
#you can use any of the sorting methods. I'm going to use the internal one in python
list_units_with_index = sorted(list_units_with_index, key=lambda unit:unit[0], reverse = True)

#initiating all the variables we need
dA = []
dD = []
for i in range(0,team_count):
    dA.append([0])
    dD.append([0])
dA = insertIndex(dA)
dD = insertIndex(dD)

result = []
for i in range(0,team_count):
    result.append([])

#initiating type : 0 = Attack, 1 = Defense
type=0

#create default order: g1,g2,g3...
order=[]
for i in range(0,team_count):
    order.append(i)

team_attributes = []
for i in range(0,team_count):
    team_attributes.append([0,0])

#start assigning
for i in range(1,int(len(list_units_with_index)/team_count)+1):
    data=[]
    
    for j in range(1,team_count+1):
        data.insert(0,list_units_with_index[i*team_count-j])
    
    data=sorted(data, key=lambda unit:unit[type], reverse = True)
    
    for i in range(0,team_count):
        result[order[i]].append(data[i][2])
        new_attack = team_attributes[order[i]][0] + data[i][0]
        new_defend = team_attributes[order[i]][1] + data[i][1]
        team_attributes[order[i]]=[new_attack, new_defend]

    #get new dA and dD
    for i in range(0,team_count-1):
        dA[i+1][0] = team_attributes[i+1][0] - team_attributes[i][0]
        dD[i+1][0] = team_attributes[i+1][1] - team_attributes[i][1]

    #check which difference is bigger, and decide which attribute we are going to look at when sorting next time
    sum_dA = getSum(dA,0)
    sum_dD = getSum(dD,0)
    if (abs(sum_dA) >= abs(sum_dD)):
        type = 0
        sorted_d = sorted(dA, key=lambda unit:unit[0])
    else:
        type = 1
        sorted_d = sorted(dD, key=lambda unit:unit[0])

    #creating new order
    order = []
    for i in range(0,team_count):
        order.append(sorted_d[i][1])

#clear temporary units
for i in range(0,2):
    for index in result[i]:
        if (index == -1):
            result[i].remove(-1)

#output result
print("final result:")
print(result)
print("with attributes:")
print(team_attributes)