# Kmap-Solver-repo
def adder(a,b):
    n=len(a)
    m=len(b)
    if (abs(n-m)==1):
        p=(m if m<n else n)
        x=(a if m<n else b)
        y=(b if m<n else a)
        index=0
        sum=0
        string=""
        for i in range(p):
            if (x[i]==y[i]):
                index+=1
                sum+=1
            else:
                break
        for i in range(0,index-1):
            string=string+y[i]
        if (x[index]=="'"):
            for i in range(index,p):
                if (x[i+1]==y[i]):
                    string=string+y[i]
                    sum+=1
        if sum==p:
            return string
        else:
            return a
    return a

#p=expanded region
#q=cell 
# this function true if q lies in p else return false
def check_pq(p,q):
    for i in range(0,len(p)):
        if p[i]!="'":
            if p[i] not in q:
                return False
        else:
            ind=q.index(p[i-1])
            if (ind+1)>(len(q)-1):
                return False
            elif q[ind+1]!="'":
                return False

    for j in range(0,len(q)):
        if q[j]!="'":
            if q[j] in p and j<(len(q)-1):
                if (q[j+1]=="'"):
                    ind=p.index(q[j])
                    if ind==len(p)-1:
                        return False
                    elif p[ind+1]!="'":
                        return False           
    return True

# return the no of variable this function
def length(p):
    n=0
    for i in range(0,len(p)):
        if p[i]!="'":
            n+=1
    return n

#main funtion
def comb_function_expansion(func_TRUE,func_DC):
    all_terms=func_DC+func_TRUE
    new_terms=[]
    for i in range(0,len(func_TRUE)):
        check=[]
        for j in range(0,len(all_terms)):
            if (func_TRUE[i]!=all_terms[j]):
                check.append(adder(func_TRUE[i],all_terms[j]))
        check=list(set(check))
        if len(check)==1:
            new_terms+=check
        else:
            if func_TRUE[i] in check:
                check.remove(func_TRUE[i])
                new_terms=new_terms+check
            else:
                new_terms+=check
    # definition of expand
    def expand(new_terms):
        terms=[]
        for i in range(0,len(new_terms)):
            check=[]
            for j in range(0,len(new_terms)):
                if (i!=j):
                    added_terms=(adder(new_terms[i],new_terms[j]))
                    if check.count(added_terms)==0:
                        check.append(added_terms)
            if len(check)==1:
                terms.append(check[0])
            else:
                if new_terms[i] in check:
                    check.remove(new_terms[i])
                    terms=terms+check
                else:
                    terms+=check
        if (len(new_terms)==1):
            terms=new_terms    
        # condition for termination
        if (terms==new_terms):
            terms=list(set(terms))
            valid_possible_regions=[]
            for i in range(0,len(terms)):
                for j in range(0,len(terms)-1):
                    if (length(terms[j])>length(terms[j+1])):
                        terms[j],terms[j+1]=terms[j+1],terms[j]

            region_to_be_deleted=[]
            index=-1
            while(len(terms)>=abs(index)):
                region=terms[index]
                cell_contained_in_i_region=[]
                for j in range(0,len(func_TRUE)):
                    if check_pq(region,func_TRUE[j]):
                        cell_contained_in_i_region.append(func_TRUE[j])
                while(len(cell_contained_in_i_region)>0):
                    cell=cell_contained_in_i_region[-1]
                    remove_cell=0
                    for k in range(0,len(terms)):
                        if(terms[k]!=region):
                            if check_pq(terms[k],cell):
                                cell_contained_in_i_region.remove(cell)
                                remove_cell=1
                                break
                    if (remove_cell==0):
                        break
                if (len(cell_contained_in_i_region)==0):
                    region_to_be_deleted.append(region)
                    terms.remove(region)
                else:
                    index-=1
            print("last expanded terms with deleted extra terms",terms)
            return terms
        return expand(terms)
    return expand(new_terms)
        

comb_function_expansion(["ab'c'de'fgh'i'j'", "ab'c'de'fgh'ij", "ab'c'de'fgh'ij'"
