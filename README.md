# segment-tree-with-lazy-propogation-for-coin-fliping


// Online C++ compiler to run C++ program online
#include <iostream>
#include<bits/stdc++.h>
using namespace std;

class ST{
    vector<int>seg, lazy;
    public:
    ST(int n){
        seg.resize(4*n+1);
        lazy.resize(4*n+1);
    }
    
    void build(int ind, int left, int right, int arr[]){
        if(left==right){
            seg[ind]=arr[left];
            return;
        }
        
        int mid =(left+right)>>1;
        build(2*ind+1, left, mid, arr);
        build(2*ind+2, mid+1, right, arr);
        seg[ind] = seg[2*ind+1] + seg[2*ind+2];
        
    }
    void update(int ind, int left, int right, int l, int r, int val){
        if(lazy[ind]!=0){
            seg[ind] = (right-left+1)-seg[ind];
            if(left!=right){
                lazy[2*ind+1]= !lazy[2*ind+1];
                lazy[2*ind+2]= !lazy[2*ind+2];
                
            }
            lazy[ind]=0;
        }
        
        if(right<l || r<left){
            return;
        }
        
        if(left>=l && right<=r){
            seg[ind]=(right-left+1)-val;
            if(left!=right){
                lazy[2*ind+1]=!lazy[2*ind+1];
                lazy[2*ind+2]=!lazy[2*ind+2];
            }
            return;
        }
        
        int mid = (left+right)>>1;
        update(2*ind+1, left, mid, l, r, val);
        update(2*ind+2, mid+1, right, l, r, val);
        seg[ind] = seg[2*ind+1]+seg[2*ind+2];
        
        
    }
    
    int query(int ind, int left, int right, int l, int r){
        if(lazy[ind]!=0){
            seg[ind]=(right-left+1)-seg[ind];
        
        if(left!=right){
            lazy[2*ind+1]= !lazy[2*ind+1];
            lazy[2*ind+2] = !lazy[2*ind+2];
        }
        lazy[ind]=0;
            
        }
        
        // No overlapping
        if(right<l || r< left)return 0;
        
        // complete ovelapping
        
        if(left>=l && right<=r) return seg[ind];
        
        
        
        // partial overlapping
        int mid = (left+right)>>1;
        int leftVal = query(2*ind+1, left, mid, l, r);
        int rightVal = query(2*ind+2, mid+1, right, l, r);
        // seg[ind] = seg[2*ind+1] + seg[2*ind+2];
        return (leftVal + rightVal);
        
    }
    
};

int main(){
    int n;
    cin>>n;
    int arr[n];
    for(int i=0;i<n;i++){
        cin>>arr[i];
    }
    ST st(n);
    
    st.build(0, 0, n-1, arr);
    
    int q;
    cin>>q;
    while(q--){
        int type;
        cin>>type;
        if(type==1){
            int l, r, val;
            cin>>l>>r>>val;
            st.update(0,0, n-1, l, r, val);
            
        }
        else{
            int l, r;
            cin>>l>>r;
            cout<<st.query(0,0, n-1, l, r)<<endl;
        }
    }
    return 0;
}
