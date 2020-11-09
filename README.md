function Calculate( players, teams, roles, limit, size, lolimit, total, total_good, good_percent, combinations) 
{
    function factorial( i ) 
    {
	if ( i == 1 || i == 0 ) return 1;
	var ret = 1;
	for( var j=1;j<=i;++j){
		ret*=j;
    }
    return ret;
    
}

function underLimits(target) {
    
    var curr_limit = target.reduce(function(sum, current) {
        return sum + current.limit;
    }, 0);
    if (curr_limit > limit || curr_limit < lolimit)
        return false;

    var text = target.reduce(function(text, current) {
        return text + current.team + ';' + current.role + ';';
    }, '');
    var bad_teams = teams.some(function(elem){
        var occurs = (text.match(new RegExp(elem.value, 'g')) || []).length;
        return occurs < elem.min || occurs > elem.max; 
    });
    if (bad_teams)
        return false;
    
    var bad_roles = roles.some(function(elem){
        var occurs = (text.match(new RegExp(elem.value, 'g')) || []).length;
        return occurs < elem.min || occurs > elem.max; 
    });
    if (bad_roles)
        return false;
    
    return true;
}

function recurse(source, target, size) {
    for(var i = 0; i < source.length; ++i) {
        target.push(source[i]);
        if (target.length == size) {
            if (underLimits(target))
                output(target);
        }
        else
            recurse(remakesource(source, i), target, size);
        target.pop();
    }
}

function remakesource(source, index) {
    var newsource = source.slice();
    newsource.splice(0, index+1);
    return newsource;
}

var t_total = 0;
var t_total_good = 0;

function output(target, total) {
    var rec = combinations.AddNewRecord();
    rec.players = target.map(function(elem) {return elem.team + ' - ' + elem.name; }).join(', ');
    var text = target.reduce(function(text, current) {
        return text + current.team + ';' + current.role + ';';
    }, '');
    rec.teams = teams.map(function(elem){
        return elem.value + ': ' + (text.match(new RegExp(elem.value, 'g')) || []).length;
    }).join(', ');
    rec.roles = roles.map(function(elem){
        return elem.value + ': ' + (text.match(new RegExp(elem.value, 'g')) || []).length;
    }).join(', ');
    rec.limit = target.reduce(function(sum, current) {
        return sum + current.limit;
    }, 0);
    t_total_good++;
}

if (size > players.length)
    return;

recurse(players, [], size);

t_total = factorial( players.length ) / ( factorial( size ) * factorial( players.length - size ) );

total.SetValue(t_total);
total_good.SetValue(t_total_good);
good_percent.SetValue(t_total_good/t_total);}
